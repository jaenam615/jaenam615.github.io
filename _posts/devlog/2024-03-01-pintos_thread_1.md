---
layout: post
title: "핀토스 프로젝트 1 - Threads (1)"
date: 2024-03-01 20:30:00 +0900
categories: [devlog, pintos, jungle]
---
* toc
{:toc}

## 핀토스

7주차까지 어찌어찌 생존해 드디어 전산학 과정의 꽃이라고 불리는 운영체제 주차로 넘어왔다.  
크래프톤 정글이 갖는 다른 교육들과의 차이점, 핀토스.    

이번엔 정말로 나와 우리 팀의 힘으로 해보고 싶었고, 마침 팀원들의 생각도 같았기에  
정답 코드는 물론이고, 직접 제공되지 않은 외부 영상 또는 다른 블로그를 가능한 참고하지 않고 진행해보기로 했다.  

그러나 웬걸, 처음 파일들을 열어보니 그 방대한 양에 압도당했다. 
이정도 양의 코드를 하나하나 다 해석할 능력도 없을 뿐더러, 그렇게 여유부릴 기한은 아니었다고 판단했기에  
우선 제공된 깃북을 보며 내가 해야할 일을 판단하고, 전체 아키텍쳐보다는 한 문제씩 해결해나가기로 했다.

어떤 일을 완벽하게 하겠다는 말은 그 일을 안하겠다는 것과 다를 바 없다고 생각해서 바로 시작하기로 마음먹었다. 
다행히 제공된 깃북에 꽤 구체적인 설명이 있었고, 테스트 파일을 참고하여 무엇을 해야할지 어림짐작이 가능했다.
무엇보다 깃북에서 알려준 디버깅 방식인 `printf()`와 `backtrace`가 코드의 흐름을 이해하는데에 큰 도움을 주었다. 

### Alarm Clock

첫 태스크는 Alarm Clock의 수정이다. 
이미 작동되는 Alarm CLock이 핀토스에 구현되어 있지만 이는 바쁜 대기 형태로 구현되어 있었다.  
여기서 바쁜 대기란, 원하는 컴퓨터 자원을 얻기 위해 기다리는 것이 아닌, 권한을 얻을 때 까지 계속 확인하는 동기화 방식이다.  

```c
void
timer_sleep (int64_t ticks) {
	int64_t start = timer_ticks ();

	ASSERT (intr_get_level () == INTR_ON);
	while (timer_elapsed (start) < ticks)
		thread_yield ();
}

```
기존 timer_sleep 함수
{:.figcaption}

이 문제에서 가장 개념을 잡기 어려웠던 부분은, 시간이 다 될때까지 기다리는 것이 아니라 진짜 알람처럼 특정한 시간이 되면 호출하여 부르게 해야 한다는 점이었다.  
이를 이해하기까지 시간이 꽤 걸렸지만, 이해하고 나서부터는 큰 어려움 없이 진행했다.  

thread 구조체에 '일어날'시간을 지정해줄 변수를 추가하고, `thread_sleep` 함수를 만들어 `timer_sleep`에서 이를 호출하게 했다.  
`sleep_list`라는 리스트를 만들어 `thread_sleep`함수에서 해당 리스트에 일어나는 시간이 빠른 쓰레드 순으로 정렬되게 넣어줬다.  
이후, `timer_interrupt`함수에서 `thread_wakeup` 함수를 매 틱마다 호출해 일어날 시간이 된 쓰레드가 있는지 탐색하도록 했다.  
여기서 효율을 올린 방법은, 정렬을 제대로 해주었기 떄문에 리스트의 가장 앞 원소만 확인하여 (즉, 일어날 시간이 가장 빠른 쓰레드)  
시간이 됐을 시 `ready_list`로 삽입하고 아닐 시 그대로 두고 함수를 끝내는 방식으로 구현했다.  

> Alarm Clock 기본 작동 방식<br/>
> <b> timer_sleep & thread_sleep </b>
> <li>인터럽트 비활성화
> <li>`sleep_list`에 쓰레드 삽입 및 블락
> <li>인터럽트 다시 활성화

> <b> timer_interrupt </b>
> <li>일어나야 하는 쓰레드가 있는지 확인
> <li>있다면 `sleep_tick` 초기화
> <li>인터럽트 비활성화 후 리스트에서 제거 및 언블락 
> <li>인터럽트 다시 활성화
{: .prompt-info}

## 코드
```c
struct thread {
	tid_t tid;                        
	enum thread_status status;         
	char name[16];                     
	int priority;                       
    int64_t sleep_ticks; // 해당 변수 추가

	struct list_elem elem;     

    //등등
}

void
timer_sleep (int64_t ticks) {
	
	int64_t start = timer_ticks ();
=
	ASSERT(intr_get_level() == INTR_ON);

	thread_sleep(ticks+start);
	//등등
}

void thread_sleep(int64_t ticks){

	struct thread *t = thread_current();
	enum intr_level old_level;
	t->sleep_ticks = ticks;

	old_level = intr_disable();
	list_insert_ordered(&sleep_list, &t->elem, sleep_list_order, NULL);
	thread_block();
	intr_set_level(old_level);
}


void thread_wakeup(int64_t ticks) {
  enum intr_level old_level;
  old_level = intr_disable();

    struct list_elem *waking_up = list_front(&sleep_list);
    struct thread *checker = list_entry(waking_up, struct thread, elem);

    if (checker->sleep_ticks <= (ticks)) {
      waking_up = list_pop_front(&sleep_list);
      list_push_back(&ready_list, waking_up);

  intr_set_level(old_level);
}

static bool sleep_list_order(const struct list_elem *a_, const struct list_elem *b_,
            void *aux UNUSED){
  const struct thread *a = list_entry (a_, struct thread, elem);
  const struct thread *b = list_entry (b_, struct thread, elem);

  return a->sleep_ticks < b->sleep_ticks;
}

```
---

다음 과제는 Alarm-Simultaneous 라고, 동시에 깨어나는 쓰레드들을 모두 ready_list에 넣어주는 과제였다. 
이를 해결하기 위해서는 구조를 살짝 변경해야 했는데,  
현재까지는 `sleep_list`의 가장 앞 원소만 확인했지만 이제는 여러 원소를 확인할 필요가 있었기 때문이다.  

아래와 같이 전체 코드를 while문으로 감싸준 후 시간이 되지 않은 원소를 발견할 때 탈출하는 구조로 구현했다. 
이런 방식을 채택하면 시간이 된 쓰레드를 찾으면 바로 다음의 쓰레드도 확인하기 떄문에 다수의 쓰레드가 일어날 조건이 달성되면  
모두 깨워서 `ready_list`에 넣을 수 있다.  

```c
void thread_wakeup(int64_t ticks) {
  enum intr_level old_level;
  old_level = intr_disable();

  while (!list_empty(&sleep_list)) {
    struct list_elem *waking_up = list_front(&sleep_list);
    struct thread *checker = list_entry(waking_up, struct thread, elem);

    if (checker->sleep_ticks <= (ticks)) {
      waking_up = list_pop_front(&sleep_list);
      list_push_back(&ready_list, waking_up);
    } else {
      break;
    }
  }
}
```

> <b> ALARM CLOCK KEY POINT </b>
> 효율성을 올리기 위해 `sleep_ticks`가 적은 순으로 정렬 
> 시간이 안 된 쓰레드를 찾으면 탐색 중지 
> 인터럽트를 끈 상태로 리스트를 조작하기 때문에 레이스 컨디션 회피 
{: .prompt-info}

<hr>


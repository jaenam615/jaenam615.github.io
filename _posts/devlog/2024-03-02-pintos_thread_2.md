---
layout: post
title: "핀토스 프로젝트 1 - Threads (2)"
date: 2024-03-02 20:30:00 +0900
categories: [devlog, pintos, jungle]
---
* toc
{:toc}

## 우선순위 스케줄링 

우선선순위 스케줄링이란, 쓰레드 별로 설정된 우선도(Priority)에 따라 자원 할당이 되는 스케줄링 기법이다.  
이번 과제의 구현에 앞서 항상 염두해야 할 사항 두 가지를 미리 정의했다.  

1) 더 높은 우선순위의 쓰레드가 `ready_list`에 들어오면 그 즉시 양보해야 한다.  
2) 실행중인 쓰레드의 우선순위는 언제든 바꿀 수 있지만, 이로 인해 다른 쓰레드보다 우선순위가 낮아지게 되면 즉시 양보해야 한다.  

그렇다면, 쓰레드를 생성하는 함수와 우선순위를 설정하는 함수를 수정해야 하기에 기존에 구현된 이 두 함수들을 손봤다.  

```c
tid_t
thread_create (const char *name, int priority,
		thread_func *function, void *aux) {
	struct thread *t;
	tid_t tid;

	ASSERT (function != NULL);

	t = palloc_get_page (PAL_ZERO);
	if (t == NULL)
		return TID_ERROR;

	init_thread (t, name, priority);
	tid = t->tid = allocate_tid ();

	t->tf.rip = (uintptr_t) kernel_thread;
	t->tf.R.rdi = (uint64_t) function;
	t->tf.R.rsi = (uint64_t) aux;
	t->tf.ds = SEL_KDSEG;
	t->tf.es = SEL_KDSEG;
	t->tf.ss = SEL_KDSEG;
	t->tf.cs = SEL_KCSEG;
	t->tf.eflags = FLAG_IF;

	thread_unblock (t);

    /* 새로 만드는 쓰레드의 priority가 
    기존 실행 함수의 priority보다 높은 경우 즉시 양보 */
	if (thread_get_priority() < priority){ 

		thread_yield();		
	}

	return tid;
}

void
thread_set_priority (int new_priority) {
	thread_current ()->priority = new_priority;
	
	/* Priority 새로 설정 후, 만약 이 값이 ready_list의 맨 앞 원소의 Priority보다 낮을 시 양보 */
	/* 이 방식으로 하기 위해 ready_list에 들어갈 때 우선도에 따른 내림차순 정렬 구현 */
	struct list_elem *max_elem = list_max(&ready_list, priority_scheduling, NULL);
	struct thread *next = list_entry(max_elem, struct thread, elem);
	if (thread_get_priority() < next->priority){

		thread_yield();	
	}
}

/* 우선순위에 기반한 정렬 */
bool priority_scheduling(const struct list_elem *a_, const struct list_elem *b_,
            void *aux UNUSED){
	const struct thread *a = list_entry (a_, struct thread, elem);
	const struct thread *b = list_entry (b_, struct thread, elem);

	return a->priority > b->priority;
}

```

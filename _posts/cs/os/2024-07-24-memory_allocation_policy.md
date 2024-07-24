---
title: "메모리 할당 정책"
writer: James
date: 2024-07-11 13:00:14 +0900
categories: [cs, os]
tags: [cs, os, memory, 운영체제]
pin: false
math: true
mermaid: true
image:
  path: https://www.crucial.in/content/dam/crucial/brand-assets/visual-assets/images/custom-photo/solution-application/crucial-new-technology-ddr4-motherboard.jpg.transform/small-jpg/img.jpg
---

## 메모리 할당 정책  

메모리 할당 정책은 운영체제가 메모리를 효율적으로 관리하고 할당하기 위해 사용하는 다양한 방법들을 말한다.  

프로세스나 파일에 할당할 메모리 영역을 정책에 따라 결정하게 되는데, 대표적인 메모리 할당 정책으로는 First Fit, Next Fit, Best Fit, Worst Fit이 있다. 적절한 할당 정책을 채택하면 메모리 단편화를 줄이고 메모리 사용 효율을 높이는데 도움을 준다.  

### First Fit  

First Fit 방법은 빈 공간 리스트를 메모리의 주소순으로 유지하며 할당속도를 빠르게 할 수 있는 방법으로, 프로세스가 적재될 수 있는 빈 공간 중에서 가장 먼저 발견되는 곳을 할당한다.  

다음과 같은 메모리 공간이 있다고 가정해보자:  

![image](https://github.com/user-attachments/assets/3ec4bd63-3af6-43c8-b9a9-0152195e86a1)

35MB의 메모리 공간을 요구하는 프로세스를 위해 First Fit 방법을 적용하면, 공백을 처음부터 순회한다. 공백1은 30MB의 크기이기 때문에 35MB 프로세스를 할당하기엔 충분하지 않기 때문에 사용할 수 없다. 다음 빈 공간은 공백 2로, 40MB의 크기를 가진다. 이는 35MB보다 크기 때문에 사용이 가능하다. 따라서, 프로세스는 가장 먼저 발견된 '충분한 크기'의 빈 메모리 공간인 공백2에 할당되며, 40MB에 35MB를 할당하고 남은 공간인 5MB가 공백2가 된다.  

![image](https://github.com/user-attachments/assets/ebfff84c-dda4-4ae0-ac08-6d1f70293f96)  
*프로세스 할당*  
{:figcaption}  

First Fit 방법의 장점으로는 빠른 할당과 짧은 검색 시간이 있다. 다만, 메모리의 앞부분에 내부 단편화가 발생할 수 있다는 단점이 있다.  

### Next Fit  

Next Fit 방법은 First Fit 방법으 변형으로, 할당할 때마다 처음부터 리스트를 탐색하는 것이 아닌, 이전 할당때 탐색이 끝난 지점으로부터 탐색을 시작하여 할당하는 것이다. First Fit과 같이 처음으로 발견하는 충분한 공간에 프로세스를 할당한다.  

First Fit에서 예시로 든 메모리 공간을 보자. 35MB가 할당된 이후 다시 35MB 크기의 프로세스2를 할당하고자 한다면 다음과 같이 할당될 것이다:  

![image](https://github.com/user-attachments/assets/6c0f02af-25c0-4241-b0a3-1d79cc4e7fdc)  

첫 프로세스가 할당된 위치부터 탐색을 시작하여, 충분하지 않은 공간인 5MB를 건너뛰고 37MB인 공백3에 할당된다.  

First Fit보다 균형 잡힌 메모리 분포를 유지할 수 있으나, 큰 프로세스를 할당하기 어려운 경우가 많아질 수 있다.  

### Best Fit  

Best Fit 방법은 큰 빈 공간을 최대한 많이 남겨놓기 위한 방법으로, 필요한 공간을 제공할 수 있는 빈 공간 중 가장 작은 곳을 선택하여 할당한다. 즉, 가장 적은 내부 단편화가 일어난다는 말이다.  

위의 메모리 공간에 4MB의 프로세스 3를 Best Fit 방법을 적용하여 할당해보겠다. Best Fit은 최대한 할당하려는 프로세스의 크기와 근접한 공간을 찾아야 하기 때문에 충분한 공간을 찾아도 계속 탐색을 이어나가야 한다.  

![image](https://github.com/user-attachments/assets/eeeea5ed-7092-4858-b15a-6d46cc1625ce)  

모두 탐색을 한 후, 가장 4MB에 근접한 공간인 공백 2에 할당된다.  

Best Fit은 메모리 사용의 효율성을 높힐 수 있으며, 큰 공간들을 남겨두기 때문에 추후에 할당을 요청할 큰 프로세스가 무리 없이 메모리를 할당받을 수 있다. 다만, 전체 메모리를 탐색해야 하는 점 때문에 비용이 높으며, 내부 단편화로 인해 너무 작아서 사용이 불가능해지는 메모리 공간이 많이 발생할 수 있다.  

### Worst Fit  

Worst Fit은 Best Fit과 상반되는 개념의 메모리 할당 정책이다. 할당이 가능한 공간 중 가장 큰 공간을 선택하여 할당한다. Worst-fit의 주요 목적은 작은 자투리가 남지 않게 하는 것이다. Best-fit은 큰 메모리 공간을 아껴두지만, 어떠한 프로세스도 사용하지 못하는 작은 자투리 공간을 남겨두기에 단편화가 일어난다. 반면, worst-fit 알고리즘은 큰 블록에 할당함으로 사용이 어려운 작은 조각들이 생기는 것을 방지할 수 있다.  

29MB 크기의 프로세스 4가 할당을 요청한다면 다음과 같이 할당될 것이다:  

![image](https://github.com/user-attachments/assets/c905c626-bf1d-406d-b2d3-77cb73eebce0)  

Best Fit을 사용했다면 공백 1을 할당받아 1MB의 자투리 공간이 남아 단편화가 발생하고, 이 1MB의 공간은 너무 작아 다른 프로세스에 할당하기 어려웠을 것이다. Worst Fit을 사용함으로 50MB크기의 공백5를 할당받았으며, 이는 21MB의 가용 가능한 공간을 남겨둔다.  

Worst Fit 방법을 사용하면 큰 블록을 추후에 다시 활용할 수 있으나, 너무 큰 프로세스가 할당을 요청한다면 충분한 크기의 공간이 없을 수도 있다는 단점이 있다. Best Fit과 마찬가지로 전체 공간들을 탐색해야 하기 때문에 오버헤드 또한 발생한다.  







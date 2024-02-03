---
layout: post
title: "가상화"
date: 2024-02-04 12:52:00 +0900
categories: Computer Science
---

가상화란 컴퓨터 리소스의 추상화를 의미한다.

추상화라는 단어가 CS에는 매우 자주 등장하는 듯 한데, 개인적으로 무언가를 추상화 하려면 이미지가 중요하다고 생각해서 이미지를 찾아보니 이런 걸 찾았다:

<img></img>

가상화란,
하나의 물리 리소스를 여러개의 논리 리소스처럼 기능 시키거나,
여러 개의 물리 리소스를 하나의 논리 리소스처럼 기능케 한다.

이게 무슨 말이냐면,
하나의 실물 컴퓨팅 자원을 마치 여러 개인 것처럼 가상으로 쪼개서 사용하거나,
여러 개의 실물 컴퓨팅 자원들을 묶어서 하나의 자원인 것처럼 사용하겠다는 것이다.

{% highlight ruby %}
def print_hi(name)
puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

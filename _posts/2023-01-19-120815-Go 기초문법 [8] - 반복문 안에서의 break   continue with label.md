---
layout: post
title: Go 기초문법 [8] - 반복문 안에서의 break & continue with label
subtitle: 반복문 안에서의 break & continue with label
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [golang]
tags: [golang]
sidebar: [article-menu,category-list] 
---
## 반복문- break & continue with label

<aside>
💡<br>
break - 반복문에서 break label 코드가 실행되면, 해당 label을 완전히 벗어남<br>
continue - 반복문에서 continue label 코드가 실행되면, label 밑 가장 처음 반복문의 루프를 넘김

</aside>

### break label

```go
// EX1)
package main

import "fmt"

func main() {
	Loop1:
	   for i := 0; i < 3; i++ {
			for j := 0; j < 3; j++ {
				if i == 1 && j == 1 {
					break Loop1
				}
				fmt.Println(i,j)
			}
		}
	fmt.Println("END")
	
}

```

- i가 1이고 j가 1이 될 때 break Loop1을 만남
- Loop1을 빠져나오고, Loop1이 있는 곳으로 이동해 다음 명령어 `fmt.Println(”END”)` 실행
   <details>
   <summary>실행결과</summary>
   <div markdown="1">
   ```
   0 0 
   0 1
   0 2
   1 0   
   END
   ```
   </div>
   </details><br>

    

```go
// EX2)

package main

import "fmt"

funk main() {
		for i := 0; i < 3; i++{
			Loop2:
				for j := 0; j < 3; j++{
					for k :=0; k < 3; k++{
						if i == 1 && j == 1 && k == 1{
							break Loop2
						}
						fmt.Println(i,j,k)
					}
				}
		}
	fmt.Println("END")
```

- i, j, k 가 모두 1이 될 때 break Loop2를 만남
- Loop2를 빠져나오고, Loop2가 있는 곳으로 이동
- 다음 명령어 실행 : 다음 for루프 (i=2)부터 시작
   <details>
   <summary>실행결과</summary>
   <div markdown="1">
   ```
    0 0 0
    0 0 1
    0 0 1
       …
    1 1 0
    2 0 0
    2 0 1
       …
    2 2 2
    END
   ```
   </div>
   </details><br>



### continue label

```go
// EX1)
Loop3:
	for i :=0; i<3; i++ {
		for j :=0; j<3; j++ {
			if i == 1 && j == 1 {
				continue Loop3
			}
			fmt.Println(i,j)
		}
	}
```

- i가 1이고, j가 1이 될 때 continue Loop3을 만남
- Loop3 밑 첫 반복문의 루프를 넘기고( 위 예시의 경우,  i가 1인 경우), Loop3이 있는 곳으로 이동
- 다음 루프 (i=2)부터 재개
   <details>
   <summary>실행결과</summary>
   <div markdown="1">
   ```
    0 0
    0 1
    0 2
    1 0
    2 0
    2 1
    2 2
   ```
   </div>
   </details><br>


    

```go
// EX2)

for i := 0; i < 3; i++ {
   Loop4:
		for j := 0; j < 3; j++ {
			for k :=0; k < 3; k++ {
				if i == 1 && j == 1 && k == 1 {
					continue Loop4
				}
				fmt.Println(i,j,k)
			}
		}
}
```

- i, j, k가 모두 1이 될 때 break Loop4를 만남
- Loop4 밑 첫 반목문의 루프를 넘기고( 위 예시의 경우, j가 1인 경우), Loop4가 있는 곳으로 이동
- 다음 루프 (i=1, j=2, k=0)부터 재개
   <details>
   <summary>실행결과</summary>
   <div markdown="1">
   ```
   0 0 0
   0 0 1
   0 0 2
      …
   1 1 0
   1 2 0
   1 2 1
   1 2 2
   2 0 0
      …
   2 2 2
   ```
   </div>
   </details><br>



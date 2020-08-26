# Today I learned



### Review of git about  [pro git & URL참고][https://ohgyun.com/711]

##### (3시간 30분 소요...)

1. 프로젝트에서 내 리포를 서브모듈로 추가
2. 프로젝트 안에 있는 서브모듈에서 커밋하면 = 내 리포에 반영 
   * (프로젝트 내에 있는 서브모듈을 업데이트 하고 싶으면 `2번` 내용을 먼저 실행해야 함)
3. 2번 내용 실행 후 프로젝트 .git에서 커밋 이후 푸시하면 내 리포에 반영된 커밋 내용을 그대로 프로젝트에도 반영 
   * (부모 repo commit 메시지는  부모 repo에서 commit한 내용으로 반영되고, 서브모듈에 들어있는 commit 메시지는 자식 repo에서 commit 메시지로 반영된다.)



### 네이버 부스트캠프 **Final mission** 강의 수강 및 완료

1. edwith.org [CS50][https://www.edwith.org/boostcourse-cs-050/joinLectures/41307]: 6주차(C언어 Data Structures) 강의 수강 및 Quiz 풀이

   * `malloc` & `포인터` : 같은 포인티를 가리키도록 설정

     ```c
     int main(void)
     {
         int *x;
         int *y;
     
         x = malloc(sizeof(int));
     
         *x = 42;
        
         y = x
        
         *y = 13;
     }
     ```

   * `realloc` : 부족한 메모리를 malloc 함수 대신 realloc으로 재설정

     ```c
     #include <stdio.h>
     #include <stdlib.h>
     
     int main(void)
     {
         int *list = malloc(3 * sizeof(int));
         if (list == NULL)
         {
             return 1;
         }
     
         list[0] = 1;
         list[1] = 2;
         list[2] = 3;
     
         // tmp 포인터에 메모리를 할당하고 list의 값 복사
         int *tmp = realloc(list, 4 * sizeof(int));
         if (tmp == NULL)
         {
             return 1;
         }
     
         // list가 tmp와 같은 곳을 가리키도록 지정
         list = tmp;
     
         // 새로운 list의 네 번째 값 저장
         list[3] = 4;
     
         // list의 값 확인
         for (int i = 0; i < 4; i++)
         {
             printf("%i\n", list[i]);
         }
     
         //list 의 메모리 초기화
         free(list);
     }
     ```

   * `linked list` (연결리스트)

     ```c
     // 구조체의 정식명칭
     typedef struct node
     {
         int number;
         // 재귀함수처럼 호출: next포인터를 이용해 연이어 다음 주소로 가기위해 
         struct node *next;
     }
     // 구조체의 별칭
     node;
     ```

   * code 구현

     ```c
     #include <stdio.h>
     #include <stdlib.h>
     
     //연결 리스트의 기본 단위가 되는 node 구조체를 정의합니다.
     typedef struct node
     {
         //node 안에서 정수형 값이 저장되는 변수를 name으로 지정합니다.
         int number; 
     
         //다음 node의 주소를 가리키는 포인터를  *next로 지정합니다.
         struct node *next;
     }
     node;
     
     int main(void)
     {
         // list라는 이름의 node 포인터를 정의합니다. 연결 리스트의 가장 첫 번째 node를 가리킬 것입니다. 
         // 이 포인터는 현재 아무 것도 가리키고 있지 않기 때문에 NULL 로 초기화합니다.
         node *list = NULL;
     
         // 새로운 node를 위해 메모리를 할당하고 포인터 *n으로 가리킵니다.
         node *n = malloc(sizeof(node));
         if (n == NULL)
         {
             return 1;
         }
     
         // n의 number 필드에 1의 값을 저장합니다. “n->number”는 “(*n).numer”와 동일한 의미입니다. 
         // 즉, n이 가리키는 node의 number 필드를 의미하는 것입니다. 
         // 간단하게 화살표 표시 ‘->’로 쓸 수 있습니다. n의 number의 값을 1로 저장합니다.
         n->number = 1;
     
         // n 다음에 정의된 node가 없으므로 NULL로 초기화합니다.
         n->next = NULL;
     
         // 이제 첫번째 node를 정의했기 떄문에 list 포인터를 n 포인터로 바꿔 줍니다.
         list = n;
     
         // 이제 list에 다른 node를 더 연결하기 위해 n에 새로운 메모리를 다시 할당합니다.
         n = malloc(sizeof(node));
         if (n == NULL)
         {
             return 1;
         }
     
         // n의 number와 next의 값을 각각 저장합니다.
         n->number = 2;
         n->next = NULL;
     
         // list가 가리키는 것은 첫 번째 node입니다. 
         //이 node의 다음 node를 n 포인터로 지정합니다.
         list->next = n;
     
         // 다시 한 번 n 포인터에 새로운 메모리를 할당하고 number과 next의 값을 저장합니다.
         n = malloc(sizeof(node));
         if (n == NULL)
         {
             return 1;
         }
     
         n->number = 3;
         n->next = NULL;
     
         // 현재 list는 첫번째 node를 가리키고, 이는 두번째 node와 연결되어 있습니다. 
         // 따라서 세 번째 node를 더 연결하기 위해 첫 번째 node (list)의 
         // 다음 node(list->next)의 다음 node(list->next->next)를 n 포인터로 지정합니다.
         list->next->next = n;
     
         // 이제 list에 연결된 node를 처음부터 방문하면서 각 number 값을 출력합니다. 
         // 마지막 node의 next에는 NULL이 저장되어 있을 것이기 때문에 이 것이 for 루프의 종료 조건이 됩니다.
         for (node *tmp = list; tmp != NULL; tmp = tmp->next)
         {
             printf("%i\n", tmp->number);
         }
     
         // 메모리를 해제해주기 위해 list에 연결된 node들을 처음부터 방문하면서 free 해줍니다.
         while (list != NULL)
         {
             node *tmp = list->next;
             free(list);
             list = tmp;
         }
     }
     ```

     ※ linked list를 쓰면 다른 주소의 메모리 접근에 유리하지만 메모리의 임의 접근이 안된다.
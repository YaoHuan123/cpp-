#include <unistd.h>  
#include <pthread.h>  
#include <stdio.h>  
volatile int flag=10; //这里是否由volatile, 结果不同  
void* wait(void* param) {  
  int count = 1;  
  while ( flag != count ) {  
    count = ~count;  
  }  
  printf("wait\n");  
}  
void* wake(void* param) {  
  flag = 1;  
  printf("wake\n");  
}  
int main () {  
  pthread_t t[2];  
  pthread_create(&t[0], NULL, wait, NULL);  
  sleep(1);  
  pthread_create(&t[1], NULL, wake, NULL);  
  while(1);  
}  

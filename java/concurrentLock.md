**synchronized** : 암묵적인 락 - 동기화 하고자 하는 블럭, 메서드를 감싸서 락을 건다.

어느부분이 락인지 명확하지 않아서 암묵적인 락이라고 한다.

```java
List<String> list = new ArrayList<String>();
private boolean threadB(){
  synchronized(list){
    list.add("lock");
  }  
}
```



**concurrent.locks.ReentrantLock** : 명시적인 락

다중 스레드가 공유하는 주요 컬렉션 대신 완전히 독립적인 락을 채택하여 구현한다.

```java
Lock reentrantLock = new ReentrantLock();
List<String> list = new ArrayList<String>();

private boolean A(){
	try{
		reentrantLock.lock();
		list.add("Lock");
	}
	finally{
		reentrantLock.unlock();
	}
}
```

lock() : 락을 획득한다. 한번 락이 획득되면 다른 쓰레드는 이 락이 풀릴때까지 락블록 내부를 실행할 수 없다 (unlock으로 풀릴때까지 기다린다.)



**ReentrantLock 사용하는 경우 : synchronized보다 복잡한 경우**

- synchronized :  스레드간의 락 획득 순서를 보장하지 않는다 (즉 계속해서 획득을 못하는 스레드가 있을 수도 있다는 것이다) : 불공정 방법
- ReentrantLock : 메서드를 이용해 순서를 보장해주도록 할 수 있다 : 공정방법
- 코드가 여러 컬렉션이 얽혀있을 때.
- 대기 상태의 락에 대한 인터럽트를 걸어야할 때
- 락을 획득하려고 대기중인 스레드의 상태를 받아야할 때
- 단점 : try/finally 블록이 무조건 들어가서 코드가 지저분해지는 단점이 있다 



ReentrantLock 

- FairSync

  ```java
      /**
       * Sync object for fair locks
       */
      static final class FairSync extends Sync {
          private static final long serialVersionUID = -3000897897090466540L;
  
          final void lock() {
              acquire(1);
          }
  
          /**
           * Fair version of tryAcquire.  Don't grant access unless
           * recursive call or no waiters or is first.
           */
          protected final boolean tryAcquire(int acquires) {
              final Thread current = Thread.currentThread();
              int c = getState();
              if (c == 0) {
                  if (!hasQueuedPredecessors() &&
                      compareAndSetState(0, acquires)) {
                      setExclusiveOwnerThread(current);
                      return true;
                  }
              }
              else if (current == getExclusiveOwnerThread()) {
                  int nextc = c + acquires;
                  if (nextc < 0)
                      throw new Error("Maximum lock count exceeded");
                  setState(nextc);
                  return true;
              }
              return false;
          }
      }
  
  
  ```

- NonFairSync

  ```java
  /**
       * Sync object for non-fair locks
       */
      static final class NonfairSync extends Sync {
          private static final long serialVersionUID = 7316153563782823691L;
  
          /**
           * Performs lock.  Try immediate barge, backing up to normal
           * acquire on failure.
           */
          final void lock() {
              if (compareAndSetState(0, 1))
                  setExclusiveOwnerThread(Thread.currentThread());
              else
                  acquire(1);
          }
  
          protected final boolean tryAcquire(int acquires) {
              return nonfairTryAcquire(acquires);
          }
      }
  ```

  
# PAUSE

spin lock 实现中 while 忙等造成 CPU 空转，Intel 提供 PAUSE 指令，在抢锁失败时让渡几十到几百个周期给另一个线程，提高整体利用率。

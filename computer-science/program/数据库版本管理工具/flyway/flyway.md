#  JVM                                                                   
## contents                                                                
- [mmap](#mmap)                                                        

#mmap
  Native memory allocation (mmap) failed to map xxx bytes for committing reserved memory
  Native memory allocation (malloc) failed to allocate 32744 bytes for ChunkPool::allocate
  网上说要设置:https://blog.csdn.net/byg184244735/article/details/79832217
    
    JAVA_OPTS=-Xss128k -Xms256m -Xmx512m -XX:PermSize=64m -XX:MaxPermSize=1024m
  其实就是已经有个jdk在内存中,直接杀掉就OK.windows下看任务管理器的进程。
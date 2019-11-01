# git问题与方案                                                                  
## contents                                                                
- [unmergedFiles](#unmergedFiles)                                                        

#Method
## unmergedFiles 
   Pull is not possible because you have unmerged files
   网上说: https://blog.csdn.net/u012426959/article/details/78944001
   本地的push和merge会形成MERGE-HEAD(FETCH-HEAD), HEAD（PUSH-HEAD）这样的引用。HEAD代表本地最近成功push后形成的引用。MERGE-HEAD表示成功pull后形成的引用。可以通过MERGE-HEAD或者HEAD来实现类型与svn revet的效果。

    解决：
    1.将本地的冲突文件冲掉，不仅需要reset到MERGE-HEAD或者HEAD,还需要–hard。没有后面的hard，不会冲掉本地工作区。只会冲掉stage区。

    git reset –hard FETCH_HEAD

    2.git pull就会成功。

    其实我认为,还是不能解决的话，就直接再建立新目录，重新进行git clone,然后将原目录修改的代码覆盖到这个新目录。这样总能解决类似的问题。
#  CollectionSorted                                                                   
## contents                                                                
- [TreeMap](#TreeMap)                                                        

#TreeMap
  按key字母序号排列。

#HashMap
  HashMap加入数据后，会自动根据首字母排序
  https://blog.csdn.net/samyang1/article/details/80452096

#LinkedHashMap
  按添加先后顺序排列


#按实际需要排序 
##Collections.sort(xx排序类)
    对于是否有排序方法的的集合，都可以排序
    https://www.cnblogs.com/huangdabing/p/9252029.html

        public class MapSortTest {
    　　public static void main(String[] args) {
    　　　　Map<String,String> stu=new TreeMap<>();//用TreeMap储存
    
    　　　　// Map<String,String> stu=new HashMap<>();//用HashMap储存
    
    　　　　stu.put("apple", "55");
    　　　　stu.put("boy", "32");
    　　　　stu.put("cat", "22");
    　　　　stu.put("dog", "12");
    　　　　stu.put("egg", "11");
    
    　　　　//1：把map转换成entryset，再转换成保存Entry对象的list。
    　　　　List<Map.Entry<String,String>> entrys=new ArrayList<>(stu.entrySet());
    　　　　//2：调用Collections.sort(list,comparator)方法把Entry-list排序
    　　　　Collections.sort(entrys, new MyComparator());
    　　　　//3：遍历排好序的Entry-list，可得到按顺序输出的结果
    　　　　for(Map.Entry<String,String> entry:entrys){
    　　　　　　System.out.println(entry.getKey()+","+entry.getValue());
    　　　　　　　　}
    　　　　　　}
    　　}
    
    　　//自定义Entry对象的比较器。每个Entry对象可通过getKey()、getValue()获得Key或Value用于比较。换言之：我们也可以通过Entry对象实现按Key排序。
    　　class MyComparator implements Comparator<Map.Entry>{
    　　　　public int compare(Map.Entry o1, Map.Entry o2) {
    　　　　　　return ((String)o1.getValue()).compareTo((String)o2.getValue());
    　　　　　　} 
    　　　　}

##TreeMap排序方法
    有自己的排序方法，可以重写。
     
        public class MapSortTest {
    
    public static void main(String[] args) {
    　　Map<String,String> stu=new TreeMap<>(new MyComparator());//传进来一个key的比较器对象来构造treemap
    　　stu.put("apple", "55");
    　　stu.put("boy", "32");
    　　stu.put("cat", "22");
    　　stu.put("dog", "12");
    　　stu.put("egg", "11");
    　　//map的遍历：把key抽取出来用set存放，然后用迭代器遍历keyset，同时用map.get(KEY)获取key所对应的value。
    
    　　Set<String> keySet=stu.keySet();
    　　Iterator it=keySet.iterator();
    　　while (it.hasNext()) {
    　　　　String next = (String)it.next();
    　　　　System.out.println(next+","+stu.get(next)); 
    　　　　　　} 
    　　}
    }
    
    //定义key的比较器，比较算法根据第一个参数o1,小于、等于或者大于o2分别返回负整数、0或者正整数，来决定二者存放的先后位置：返回负数则o1在前，正数则o2在前。
    class MyComparator implements Comparator<String>{
    　　public int compare(String o1, String o2) {
    　　　　return o1.compareTo(o2); 
    　　}
    }
 
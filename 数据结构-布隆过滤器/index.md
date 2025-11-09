# 布隆过滤器


## ***布隆过滤器***

1. ***引出***
   
   - *对于`hashtable`，我们存放一个数据，会先进行哈希取模，然后决定放在哪一个桶，随后就会把这个数据放进🪣中，**对于哈希表来说，存放的数据是准备完整的***
   - *我们再来想一个场景，如果我们不需要完整的数据，我们只是想判断一个这个数据到底存不存在，这个时候哈希表其实不太适合，其实可以做到的，但是终归不是完美适合这个场景 所以我们需要一个更高效的数据结构*
   - *布隆过滤器 他lei了*

2. ***什么是布隆过滤器***
   
   - *他是一个很长的 **二进制向量** 和 一系列**随机映射函数***
   - *布隆过滤器可以用于检索一个元素是否在一个集合中*

3. ***布隆过滤器的优点***
   
   - *空间效率和时间效率是远远超过一般的数据结构*

4. ***布隆过滤器的缺点***
   
   - *有一定的误别率和删除困难*

5. ***布隆过滤器的图示***
   
   - ![布隆过滤器](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242322595.png)

6. ***布隆过滤器的误别***
   
   - ![布隆过滤器的误别示例图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220730192741415.png)

7. ***布隆过滤器的 golang实现***
   
   - ```go
     package main
     
     import (
         "fmt"
         "github.com/bits-and-blooms/bitset"
     )
     
     //设置哈希数组默认大小为16
     const DefaultSize = 16
     
     //设置种子，保证不同哈希函数有不同的计算方式
     var seeds = []uint{7, 11, 13, 31, 37, 61}
     
     //布隆过滤器结构，包括二进制数组和多个哈希函数
     type BloomFilter struct {
         // 使用第三方库
         set *bitset.BitSet
         // 指定长度为6
         hashFuncs [6]func(seed uint, value string) uint
     }
     
     //构造一个布隆过滤器，包括数组和哈希函数的初始化
     func NewBloomFilter() *BloomFilter {
         bf := new(BloomFilter)
         bf.set = bitset.New(DefaultSize)
     
         for i := 0; i < len(bf.hashFuncs); i++ {
             bf.hashFuncs[i] = createHash()
         }
         return bf
     }
     
     //构造6个哈希函数，每个哈希函数有参数seed保证计算方式的不同
     func createHash() func(seed uint, value string) uint {
         return func(seed uint, value string) uint {
             var result uint = 0
             for i := 0; i < len(value); i++ {
                 result = result*seed + uint(value[i])
             }
             //length = 2^n 时，X % length = X & (length - 1)
             return result & (DefaultSize - 1)
         }
     }
     
     //添加元素
     func (b *BloomFilter) add(value string) {
         for i, f := range b.hashFuncs {
             //将哈希函数计算结果对应的数组位置1
             b.set.Set(f(seeds[i], value))
         }
     }
     
     //判断元素是否存在
     func (b *BloomFilter) contains(value string) bool {
         //调用每个哈希函数，并且判断数组对应位是否为1
         //如果不为1，直接返回false，表明一定不存在
         for i, f := range b.hashFuncs {
             //result = result && b.set.Test(f(seeds[i], value))
             if !b.set.Test(f(seeds[i], value)) {
                 return false
             }
         }
         return true
     }
     
     func main() {
         filter := NewBloomFilter()
         filter.add("asd")
         fmt.Println(filter.contains("asd"))  // true
         fmt.Println(filter.contains("2222")) // 
         fmt.Println(filter.contains("155343"))
     }
     ```


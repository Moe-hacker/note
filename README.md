# 关于
沨鸾与峰峦同音，有高山仰止之意，沨，形容水声风声或其他宏大的声音；鸾，幻想中的太阳鸟，一般认为是雄性，与凤相对，而鸾随沨起，算是个美好的愿景，愿如飞鸟般自由，愿飞向心中的那个地方，愿能像鸾鸟般发出自己宏大的声音。      
算是一本半日记的东西，说实话这地方大概率不会有什么对您有用的东西，看别人的人生中发生的那些事不会怎么影响您的人生，而我所做的思考若您不认同也只不过是满纸荒唐言罢了。人生这东西无法抄袭，我们每个人都在踏着独属于自己的路。      
但我自己确实该写点什么了，再不做点什么光焦虑症都能把我逼疯的。      
github是个好东西，可以静下心来写点真正写给自己的东西了啊。      
# 初始篇：      
算是第一篇吧，实际上这段算纯技术思考了。       
打算给文章用生草的随机命名法，于是开始思考随机数生成。      
不难写一个：      
```C
#include <stdio.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdbool.h>
bool get_rand_1_bit(void)
{
        int fd = open("/dev/random", O_RDONLY);
        if (fd < 0)
                exit(1);
        uint64_t buf;
        read(fd, &buf, sizeof(buf));
        buf = buf % 2;
        return (bool)buf;
}
int main(void)
{
        uint32_t r = 0;
        for (int i = 0; i < 32; i++) {
                r += get_rand_1_bit() << i;
        }
        printf("%08x\n", r);
}
```
于是引发了一个问题：每次取64位随机数的最后一位，真的比每次取一位更随机吗？      
我想当然的使用了对二取余，但一想似乎不对，它在二进制层面不就是取二进制数据的最后一位，也就是说，他其实也只获得了一位随机数。      
然后问了GPT，它说：对于非理想随机数发生器，这种方式有一定作用（但不多），它可能克服随机数发生器的不随机性，但我认为这样不如干脆取64位中的随机位。      
更好的方法是进行哈希操作后再取一位，但显然我懒，而且也没学过。     
然后观察这个生成的值，那么这个值是均匀分布的吗？它的前4位不出现1的概率是1/16，而出现1的概率是15/16，也就是说他有15/16的概率是个8位16进制全满的大数，这也确实符合观测结果。但这看着似乎不是很随机的样子啊？      
但是，获得大数的可能性更大是不是只是因为大数更多？      
是的喵。      
事实上不难验证，值域中每个数出现的概率都是2^(-32)，思考了半天发现上面那个疑问完全是瞎想。      
有时候我们需要多一步思考，有时候多一步思考反而可能是错的。      
于是这本日记第一篇，和文学与哲学没半毛钱关系，单纯成随机数发生器的讨论了。。。       
注：bool的特性是非0即1，事实上它占一个byte，但只存储一位数据。      
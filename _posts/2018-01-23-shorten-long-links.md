---
layout: post
title: Shorten Long Links
date: 2018-01-23 00:22
author: admin
comments: true
categories: [web]
tags: [web,url]
---



主要催生事件：Twitter 140-character restriction（国内微博140字限制）。
使用短链接的好处：短，美观，更好控制更好统计客户信息，减少垃圾外链。

## Try shorten url web service

Powered by sina: http://dwz.wailian.work/ ，both domestic and global。

## Short link algorithm

短链接算法基本都是一个套路，将自增ID转为62进制:  
1. 获取该条记录的自增ID  
2. 将自增转换为62进制，并拼接网址 如：http://qetee.com/w7e   
3. 用户访问到 http://qetee.com/w7e时，提取短网址后缀 w7e   
4. 将短网址后缀转换为10进制，得到自增ID号 如：123456  
5. 使用查询该记录，进行业务逻辑处理(比如跳转)

 

## Code implementation

```c#
namespace Alphabet 
{
    public class AlphabetTest 
    {
        public static readonly string Alphabet = "abcdefghijklmnopqrstuvwxyz0123456789";
        public static readonly int Base = Alphabet.Length;

        public static string Encode(int i)
        {
            if (i == 0) return Alphabet[0].ToString();

            var s = string.Empty;

            while (i > 0)
            {  
                s += Alphabet[i % Base];
                i = i / Base;
            }

            return string.Join(string.Empty, s.Reverse());
        }

        public static int Decode(string s)
        {
            var i = 0;

            foreach (var c in s)
            {
                i = (i * Base) + Alphabet.IndexOf(c);
            }

            return i;
        }

        public static void Main(string[] args) 
        {
            // Simple test of encode/decode operations
            for (var i = 0; i < 10000; i++) 
            {
                if (Decode(Encode(i)) != i) 
                {
                    System.Console.WriteLine("{0} is not {1}", Encode(i), i);
                    break;
                }
            }
        } 
    }
}
```

### saas comparation

| service name        | pros   |  cons  | eg.|
| :--------   | :-----  | :----  | :----  |
| Bitly.com| top rated| |http://bit.ly/2eJynMi|
| Goo.gl| google's | |https://goo.gl/PkEbhG|
| TinyURL.com | customize|long|https://tinyurl.com/ezhome-hs|

## Conclusion

国际版可以用bitly，使用的人多，有访客分析  
国内暂没发现成熟的，cmcc.in  
看起来是个小作坊不太可靠，有两种选择，更倾向于第二种，数据掌握在自己的手里  
1. 跟国内新浪微博、淘宝、百度等大厂合作，大厂不轻易倒闭或暂停服务，服务可靠
2. 自己搭，技术门槛不是很高

## Reference：

* <https://stackoverflow.com/questions/742013/how-to-code-a-url-shortener>
* <https://www.lifewire.com/shortening-long-links-3486603>
* <https://gist.github.com/dgritsko/9554733>




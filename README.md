<<<<<<< HEAD
爬虫必备工具：cheerio

cheerio是jquery核心功能的一个快速灵活而又简洁的实现，主要是为了用在服务器端需要对DOM进行操作的地方。 大家可以简单的理解为用来解析html非常方便的工具。

 npm install cheerio

 首先我们请求http协议，通过http来拿到网页的所有数据
const https = require('https');
https.get('https://movie.douban.com/top250',function(res){
    // 分段返回的 自己拼接
    let html = '';
    // 有数据产生的时候 拼接
    res.on('data',function(chunk){
        html += chunk;
    })
    // 拼接完成
    res.on('end',function(){
        console.log(html);
    })
})
复制代码上面代码呢，大家一定要注意我们请求数据时，拿到的数据是分段拿到的,我们需要通过自己把数据拼接起来
res.on('data',function(chunk){
        html += chunk;
    })
复制代码拼接完成时 我们可以输出一下，看一下我们是否拿到了完整数据
res.on('end',function(){
        console.log(html);
    })
复制代码二、使用cheerio工具解析需要的内容
const cheerio = require('cheerio');
res.on('end',function(){
        console.log(html);
        const $ = cheerio.load(html);
        let allFilms = [];
        $('li .item').each(function(){
            // this 循环时 指向当前这个电影
            // 当前这个电影下面的title
            // 相当于this.querySelector 
            const title = $('.title', this).text();
            const star = $('.rating_num',this).text();
            const pic = $('.pic img',this).attr('src');
            // console.log(title,star,pic);
            // 存 数据库
            // 没有数据库存成一个json文件 fs
            allFilms.push({
                title,star,pic
            })
        })
复制代码可以通过检查网页源代码查看需要的内容在哪个标签下面，然后通过$符号来拿到需要的内容，这里我就拿了电影的名字、评分、电影图片



到了这时候，你会发现，node爬虫实现是非常简单的，我们只需要认真分析一下我们拿到的html数据，将需要的内容拿出来保存在本地就基本完成了

保存数据
下面就是保存数据了，我将数据保存在films.json文件中

将数据保存到文件中，我们引入一个fs模块，将数据写入文件中去
const fs = require('fs');
fs.writeFile('./films.json', JSON.stringify(allFilms),function(err){
            if(!err){
                console.log('文件写入完毕');
            }
        })
复制代码文件写入代码需要写在res.on('end')里面，数据读完->写入
写入完成，可以查看一下films.json，里面是有爬取的数据的。
下载图片
我们爬取的图片数据是图片地址，如果我们要将图片保存到本地呢？ 这时候只需要跟前面请求网页数据一样，把图片地址url请求回来，每一张图片写入到本地即可
function downloadImage(allFilms) {
    for(let i=0; i<allFilms.length; i++){
        const picUrl = allFilms[i].pic;
        // 请求 -> 拿到内容
        // fs.writeFile('./xx.png','内容')
        https.get(picUrl,function(res){
            res.setEncoding('binary');
            let str = '';
            res.on('data',function(chunk){
                str += chunk;
            })
            res.on('end',function(){
                fs.writeFile(`./images/${i}.png`,str,'binary',function(err){
                    if(!err){
                        console.log(`第${i}张图片下载成功`);
                    }
                })
            })
        })
    }
}
复制代码下载图片的步骤跟爬取网页数据的步骤是一模一样的，我们将图片的格式保存为.png

写好了下载图片的函数，我们在res.on('end')里面调用一下函数就大功告成了

=======
# node-test
简单node 爬虫
>>>>>>> fe5ab767d6cec4362eeeb3cbffd29a5a06d7f367

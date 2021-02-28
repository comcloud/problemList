## 关于使用textarea传递markdown字符内容问题

### 概要
> 我先给一个使用场景
> 我要使用`textarea`来记录`Markdown`内容，当然这个内容是需要存储到数据库之中的(这里使用`Ajax`发送数据，我们回来拿到`markdown`内容时候只需要使用第三方将内容转换一下就行)
~~~javascript
function articleFunction(isDraft) {
            let content = $('.editormd-markdown-textarea').val();
            let title = $('#title').val();
            let fatherLabel = document.getElementById("father").value;
            let sonLabel = document.getElementById("son").value;
			//构建一个文章对象
            let article = {
                content,
                title,
                isDraft,
                fatherLabel,
                sonLabel
            };
            $.ajax({
                url: '/article/saveArticle',
				//使用JSON.stringify方法将对象转换为一个json串给后端进行处理
                data: JSON.stringify(article),
                type: 'post',
                success: function (data) {
                    let parse = JSON.parse(data);
                    if (parse.success) {
                        if (parse.isDraft) {
                            alert("保存成功");
                        } else {
                            location.href = "/article/publish_success";
                        }
                    }
                }
            })
        }
~~~
这个时候我们可以了，数据传输没有问题，但是实际上是什么呢，实际上问题很大

**一步步来说明问题**
 - 1.为什么数据拿到后台我们发现实际上再拿回来时候不一样(多了一个"")
 - 2.解决上个问题时候出现一个新鲜的问题：换行符问题，在`textarea`中拿到的数据我们传递给后台可以看到每个换行符是`\n`，这个看似是没有问题的
 - 3.第三方解析不了这个`markdown`内容（他的语法是没有问题的）

### 解决问题

**问题一**：多了一个""这个很好去解释，我们在后台去拿到这个JSON串时候肯定是将他作为一个字符串处理的，既然是字符串那必定会带一个""(存储到数据库也是最外层有""，一个被它包裹的`markdown`自然无法解析)，解决方式就是将它去除
**问题三**：因为存在前两个问题，这个问题不可以解决，我们重点放在第二个问题
**问题二**：所谓的换行符到底是怎么回事

> 众所周知，`<br/>`他是作为html的换行标签，要在浏览器中换行，也就是解析为浏览器可以认识的换行符应该是用到这个标签，我们在后台拿到的换行符是`\n`，我们自己做个替换可以吗(这里我找遍了博客，去掉重复的，解决方法：传递后台的时候js做一个替换将所有换行符替换为<br>，然后从后台拿的时候再在前台转换为换行符，当然没有一点用)
倘若我把数据打印出来会发现这个所谓的换行符`\n`根本没有起到换行的作用，就好像我们自己打印一个串，这个串包含\n，他是不换行的，稍作思考只有一个情况会导致这个结果，就是`\n`被转义了，也就是在网络传输中被作为`\\n`，这样的话他不再会有换行的作用,说到这里解决办法显而易见：
**也就是做一个替换`markdownContent.replace("\\n", "\n")`，这个就好像当初我们传递一个图片的base64串，我们需要做一个替换操作：`replace(" ","+")`,否则这个图片不会有效**

------------

> 还有个解决办法，那就是不要使用`JSON.stringify`，而是直接将文章对象传递，现在我们会发现之所以会出现换行那个问题，实际上就是因为这个方法做了处理，这个方法主要作用就是将给定的value转换为一个JSON串，在WEB端前后端交互常被使用到，如果不使用这个方法就不会存在那个换行出现问题的情况，只是后台解析会稍微麻烦一点











































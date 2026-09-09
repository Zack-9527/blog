```
<%*
// ========== 不要修改这部分代码 ==========
const title = await tp.system.prompt("输入博客文章标题");
if(!title) return;
// 固定生成到 content/posts
const targetFolder = "content/posts";
// 创建文件，使用你的 hugo-post.md 模板
const newFile = await tp.file.create_new(tp.file.find_tfile("hugo-post.md"), title, true, targetFolder);
await app.workspace.getLeaf().openFile(newFile);
%>
```
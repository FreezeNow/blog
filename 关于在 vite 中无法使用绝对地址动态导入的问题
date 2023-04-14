# 关于在 vite 中无法使用绝对地址动态导入的问题
碰到一个贼无语的问题，有一个项目，需要使用虚拟键盘，找到了一个自称能在 vue 3 使用的，还能导入词库，看起来挺好，装完一用。好家伙，报错一环接一环，给我整无语了。但是市面上已经没有另外一款带中文的虚拟键盘了，只能捏鼻子下载下来自己改了。

整了半天，终于让组件能在 vue 3 + TS 下跑了，一看，我的中文呢？看了看文档，只需要引用词库就行了，我便将地址丢入组件中，然后，程序就报错了：TypeError: Failed to resolve module specifier '@/dict/baseDict.json'。

我直接一脸问号，明明这路径存在资源啊。然后我就开始搜索，一开始我直接搜了报错，发现和我的问题差之千里，就换成 TypeScript dynamic import 当关键词，搜出来一些似是而非的答案，不过也给了我启发。接下来我就使用 Vue 3 template string TypeScript dynamic import 当关键词，还是找不到答案。

但是在找的过程中，我发现使用一些规律，下面是代码：
```JavaScript
import('@/dict.json') // 正常
import('../../dict.json') // 正常
var url = '../dict.json'
import(url) // 正常

import(`${'@/dict.json'}`) // 报错
var url = '@/dict.json' 
import(url) // 报错
var url = 'dict.json'
import(`@/${url}`) // 报错
import('@/' + url) // 报错
var url = 'dict.json'
import('../../' + url) // 报错，但不是无法引用，而是 MIME 不正确

// 该案例使用了 js 而不是 json
var url = 'dict.js'
import('../../' + url) // 正常
// dict.js
import baseDict from "./baseDict.json";
export default baseDict
```
不过这已经不重要了，因为我在这问题上花了很多时间，已经和这问题犟上了。虽然我可以直接写死路径，但是废了大半天也找不到答案，我这不得给这问题解决咯再写篇博客记录一下？

接下来我转换了思路，这个报错不止最开始的 TypeError，后面还接了错误来源：runtime-core.esm-bundler.js。我去搜了搜，发现这是 vue 的 运行相关的东西，于是便搜索 runtime-core template string dynamic import。终于在[第一个链接中](https://stackoverflow.com/questions/72845337/dynamic-import-of-component-does-not-work-with-component-path-from-variable)找到了答案。

简单点说就是 vite 不支持动态导入 '@/' + 变量 这种模式，只支持相对路径。然后我去改了这个项目和另外一个 element plus 生成的路由引用组件的代码，看看能否支持这个结论，结果是支持的，只要改成 ``import(`${'@/dict.json'}`)`` 就会报错，这下思路就清晰了，只要改成相对地址就完事了。顺手给项目对应的 github 提了个 issus，希望可以给 vue 3 的中文开源环境提供一点点贡献吧（不过不知道变成包引用后，这个相对路径该写成什么样）。

之后又在 vue 2 的项目中试了试，发现在路由组件中使用 ``import(`${'@/view/home.vue'}`)`` 是可以正常运行的，和之前找到的答案有点出入，不过，以后再说8~
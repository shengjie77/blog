# Project Guidelines

这篇文章整理了一些我平时在项目开发中总结出来的最佳实践，针对的是前端项目，但是有些经验也适用于其他领域的开发。

# Directory & File

## 文件名和目录名使用小写

```tsx
-- components
   |-- button // Good
   |-- Slider // Bad
```

在我最早做 C++ 开发的时候就知道这个规范。它存在的原因主要是不同操作系统对文件名大小写有不同的处理，比如 Linux 是大小写敏感，而 Windows 和 Mac 是不敏感的。所以为了解决这个问题采用统一小写的命名规范（相比于大写，小写可读性更好）。

虽然如此，但是在实际开发中我一直没有采用，直到遇到了一些奇怪的问题。

在一次代码提交后，CI/CD 报错了，显示某个模块引用出错，报错的代码大致如下：

```tsx
import { func } from './MyFavouritefile'; // import error
// import { func } from './MyFavouriteFile'; // 正确的路径

func();
```

明明开发环境都是正常的，很让人费解。一开始我觉得是 dev 和 release 的构建不一样导致的问题，比如编译后 export 的对象不对，或者目录不正确等等。但是经过一番尝试，仍然找不到问题的根源。最终我发现是引用的文件名的大小写不对，而且当时那个文件名比较长，所以第一眼没看出来。修改后提交代码，问题就解决了。

此时我想起了这个一直被我忽视的规范。因为我开发电脑是 Mac，大小写不敏感，所以即使文件名大小写拼错了，仍然可以正常工作，而 CI/CD 的机器是 Linux 的，所以就出错了。如果你是一个不喜欢用 aotocompletion 的人，那犯这种错的几率会大大提升。

还有一件事也让我印象深刻。有一次在重构一个模块，即一个文件夹内的代码，改完后顺手把文件夹名改成全小写的了。代码部署后发现功能还是旧的，看了眼同事电脑上的代码，确实是老代码，上 gitlab 网页看了一下，明明新的代码就在那！不过仔细一看，老的代码也在，它们在两个不同的文件夹里，唯一的区别就是文件夹名的大小写不同。虽然我本地的 git 显示我重命名的操作正常完成了，但是在 gitlab 那边就是有问题的，导致 clone 下来的永远都是老的代码。

经过这两次事件，坚定了我落实这条规范的信念。

## index 文件只用于做导出

```bash
-- button
   |-- index.tsx
```

我发现许多人会习惯用上面这样的文件结构，把 button 的代码写在 `index` 文件里。这样的结构存在以下问题：

- 不利于文件搜索，在搜索时要同时输入父目录的名称才能定位到。
- 不利于扩展。比如我需要创建一个 `button-group.tsx` 的文件，并且里面的内容需要从 `button` 这个模块导出，那我就必须要在 `index.tsx` import 并 export，久而久之，这里面除了 button 的代码外， 会有越来越多的不相关代码存在，影响可读性。

综上，我建议使用下面的目录结构

```bash
-- button
   |-- button.tsx
   |-- button-group.tsx
   |-- index.ts # 只保留 import 和 export 相关代码
```

# Coding Style

## Import order

```tsx
// import 顺序如下：
// 1. 外部库
import * as React from 'react';
import * as _ from 'lodash';

// 2. 公司内部库
import { libA } from 'internal/a';
import { libB } from 'internal/b';

// 3. 本项目的代码，最好使用绝对路径
import { Button } from 'src/component/button';
import { Slider } from 'src/component/slider';

// 4. 其他资源
import a from 'assets/a.svg';
import 'style.css';
```

排序主要是按照 import 的库的稳定程度，分为外部库、内部库、本仓库代码和其他资源这几类，不同类别之间空一行。

这个规范主要可以让代码看起来更有条理一些。

## Use absolute path

```tsx
import { Button } from '../../components/button'; // Bad
import { Slider } from 'src/components/slider'; // Good

// 只有在 index.ts 里可以使用相对路径
```

使用绝对路径的优势如下：

- 更强的可读性，一眼就可以确认 import 的代码位置。
- 方便移动文件。有时候重构代码时需要移动文件，如果 import 全是绝对路径的话，一行代码都不用改。
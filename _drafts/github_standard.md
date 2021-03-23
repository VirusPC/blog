# github best practice

## Pull Request

功能分支合并进 master 分支, 必须通过 Pull Request. 可以在提交的时候，@相关人员或团队，引起他们的注意。
## Protected branch

master分支应该受到保护，不是每个人都可以修改这个分支，以及拥有审批 Pull Request 的权力。

## Issue

Issue 用于 Bug追踪和需求管理。建议先新建 Issue，再新建对应的功能分支。

功能分支总是为了解决一个或多个 Issue。功能分支的名称，可以与issue的名字保持一致，并且以issue的编号起首，比如"15-require-a-password-to-change-it"。

开发完成后，在提交说明里面，可以写上"fixes #14"或者"closes #67"。Github规定，只要commit message里面有下面这些动词 + 编号，就会关闭对应的issue。

- close
- closes
- closed
- fix
- fixes
- fixed
- resolve
- resolves
- resolved

这种方式还可以一次关闭多个issue，或者关闭其他代码库的issue，格式是username/repository#issue_number。

Pull Request被接受以后，issue关闭，原始分支就应该删除。如果以后该issue重新打开，新分支可以复用原来的名字。

## Merge节点

Git有两种合并：一种是"直进式合并"（fast forward），不生成单独的合并节点；另一种是"非直进式合并"（none fast-forword），会生成单独节点。

前者不利于保持commit信息的清晰，也不利于以后的回滚，建议总是采用后者（即使用--no-ff参数）。只要发生合并，就要有一个单独的合并节点。

## Squash 多个 commit

为了便于他人阅读你的提交，也便于cherry-pick或撤销代码变化，在发起Pull Request之前，应该把多个commit合并成一个。（前提是，该分支只有你一个人开发，且没有跟master合并过。）

## 撰写提交信息

提交commit时，必须给出完整扼要的提交信息，下面是一个范本。

```
Present-tense summary under 50 characters

* More information about commit (under 72 characters).
* More information about commit (under 72 characters).

http://project.management-system.com/ticket/123
```

第一行是不超过50个字的提要，然后空一行，罗列出改动原因、主要变动、以及需要注意的问题。最后，提供对应的网址（比如Bug ticket）。

---
参考
- [Git 工作流程 -- 阮一峰](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
- [Git 使用规范流程 -- 阮一峰](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)
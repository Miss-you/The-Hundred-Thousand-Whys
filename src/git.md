## 关于工作流的一些看法

开发工作流要与团队规模、项目结构、以及CICD相匹配，其他都是扯淡。

举个例子，比如git flow就有点扯淡，最开始接触时觉得挺美好，后来觉得它过于复杂，完全没必要，实际上应该选择合适自己项目的工作流；简而言之，开发时要有独立分支，发布时有独立分支，合入主干需要经过CI构建、静态检查以及自动化测试即可，其他根据实际情况来选择。
## jenkins

相比github actions，jenkins显得更成熟和复杂。
jenkins文档对中文的支持更加有友好。
特性更多：能够加入人工确认，支持超时，重试和完成时动作
有非常强大的插件系统

## UI
|         | github actions                                        | jenkins                                                                              |
| ------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------ |
| monitor | [meercode](https://meercode.io/monitor/)              | [build view](http://dev.devecor.cn:8080/view/image/)                                 |
|         | [actions](https://github.com/Devecor/upimage/actions) | [bule ocean](http://dev.devecor.cn:8080/blue/organizations/jenkins/upimage/activity) |


## 代码复用

|                                                                             | github actions                                                                                         | jenkins            |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------ |
| 自定义脚本`sh script.sh`                                                    | :white_check_mark:                                                                                     | :white_check_mark: |
| [扩展共享库](https://www.jenkins.io/zh/doc/book/pipeline/shared-libraries/) | :-1:                                                                                                   | :+1:               |
| marketplace                                                                 | [actions marketplace](https://github.com/marketplace?category=&query=&type=actions&verification=) :+1: |                    |

## conclusion

jenkins 看起来是一个更加成熟的工具
但是github actions在源代码旁构建流水线的便利性和不要钱的算力，更能够吸引个人开发者和开源团队

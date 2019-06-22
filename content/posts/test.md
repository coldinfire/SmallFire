| language:go |                                                              |
| ----------- | ------------------------------------------------------------ |
|             |                                                              |
|             | go:                                                          |
|             | - "1.8"  # 指定 Golang 1.8                                   |
|             |                                                              |
|             | install:                                                     |
|             | # 安装最新的 hugo                                            |
|             | - wget https://github.com/gohugoio/hugo/releases/download/v0.51/hugo_0.51_Linux-64bit.deb |
|             | - sudo dpkg -i hugo*.deb                                     |
|             | # 安装主题                                                   |
|             | - git clone https://github.com/liuzc/LeaveIt.git             |
|             | script:                                                      |
|             | # 运行 hugo 命令                                             |
|             | - hugo                                                       |
|             | after_script:                                                |
|             | # 部署                                                       |
|             | - cd ./public                                                |
|             | - git init                                                   |
|             | - git config user.name "coldinfire"                          |
|             | - git config user.email "coldinfire@163.com"                 |
|             | - git add .                                                  |
|             | - git commit -m "Update Blog By TravisCI With Build $TRAVIS_BUILD_NUMBER" |
|             | # Github Pages                                               |
|             | - git push --force --quiet "https://$GITHUB_TOKEN@${GH_REF}" master:master |
|             | # Github Pages                                               |
|             | - git push --quiet "https://$GITHUB_TOKEN@${GH_REF}" master:master --tags |
|             | env:                                                         |
|             | global:                                                      |
|             | # Github Pages                                               |
|             | - GH_REF: https://github.com/coldinfire/coldinfire.github.io.git |
|             | deploy:                                                      |
|             | provider: pages # 重要，指定这是一份 github pages 的部署配置 |
|             | skip-cleanup: true # 重要，不能省略                          |
|             | local-dir: public # 静态站点文件所在目录                     |
|             | # target-branch: master # 要将静态站点文件发布到哪个分支     |
|             | github-token: $GITHUB_TOKEN # 重要，$GITHUB_TOKEN 是变量，需要在 GitHub 上申请、再到配置到 Travis |
|             | # fqdn:  # 如果是自定义域名，此处要填                        |
|             | keep-history: true # 是否保持 target-branch 分支的提交记录   |
|             | on:                                                          |
|             | branch: master # 博客源码的分支                              |

ec717d8b4e26a5f62556d3f50984d63b6e2061a0
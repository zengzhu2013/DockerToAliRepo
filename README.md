

使用Github Action将Docker镜像转存到阿里云私有仓库，彻底解决在国内Docker镜像无法拉取的问题。

## 具体配置步骤


### 1. 配置阿里云镜像仓库
登录阿里云后，进入容器镜像服务 https://cr.console.aliyun.com/cn-hangzhou/instances<br>

#### 创建个人实例

![个人实例](/img/个人实例.png)
创建个人实例（免费），创建一个命名空间（**后面会用于环境变量ALIYUN_NAME_SPACE**）
![命名空间](/img/命名空间.png)

#### 查看访问凭证

![访问凭证](/img/访问凭证.png)

对应后续需要配置的环境变量：<br>

- 仓库地址（**ALIYUN_REGISTRY**）<br>

- 用户名（**ALIYUN_REGISTRY_USER**)<br>

- 密码（**ALIYUN_REGISTRY_PASSWORD**)<br>

  

### 2. Fork 本项目

Fork DockerToAliRepo项目<br>
#### 启动Action
进入您自己的项目，点击Action，启用Github Action工作流功能<br>

> 更多的Github Action使用细节，参考官方文档：https://docs.github.com/zh/actions

![action工作流](/img/action工作流.png)



#### 配置环境变量

进入Settings->Secret and variables->Actions->New Repository secret
![配置环境变量](/img/配置环境变量.png)
将前面步骤中出现的**四个变量**<br>
ALIYUN_NAME_SPACE，ALIYUN_REGISTRY,  ALIYUN_REGISTRY_USER，ALIYUN_REGISTRY_PASSWORD<br>
配置成环境变量<br>

参考配置如下：

```
ALIYUN_NAME_SPACE=tulingfox
ALIYUN_REGISTRY=registry.cn-hangzhou.aliyuncs.com
ALIYUN_REGISTRY_USER=fox666
ALIYUN_REGISTRY_PASSWORD=输入自己设置的密码
```



### 3. 添加镜像
打开images.txt文件，添加你想要的镜像 

- 可以加tag，也可以不用(默认latest)<br>
- 可添加 --platform=xxxxx 的参数指定镜像架构<br>
- 可使用 k8s.gcr.io/kube-state-metrics/kube-state-metrics 格式指定私库<br>
- 可使用 #开头作为注释<br>

![拉取的镜像](/img/拉取的镜像.png)
文件提交后，自动进入Github Action构建<br>

![action构建](/img/action构建.png)

### 4. 使用镜像
回到阿里云，镜像仓库，点击任意镜像，可查看镜像状态。(可以改成公开，拉取镜像免登录)<br>

![操作指南](/img/操作指南.png)

查看刚刚上传到仓库的redis镜像<br>

![成功拉取](/img/成功拉取.png)

此时就可以使用docker pull拉取redis镜像, 例如：<br>
```
docker pull registry.cn-hangzhou.aliyuncs.com/tulingfox/redis:7.4.1
```
registry.cn-hangzhou.aliyuncs.com 即 ALIYUN_REGISTRY(阿里云仓库地址)<br>
tulingfox即 ALIYUN_NAME_SPACE(阿里云镜像仓库的命名空间)<br>
redis:7.4.1 即 阿里云镜像仓库中显示的镜像名<br>

![拉取redis镜像](/img/拉取redis镜像.png)


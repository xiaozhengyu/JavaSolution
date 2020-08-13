# Spring 项目单元测试

1. 创建测试类

    ①点击待测试类的类名

    ![image-20200813154011067](markdown/Spring项目单元测试.assets/image-20200813154011067.png)

    ②使用快捷键Ctrl + Shift + T，进入测试类创建选择窗口：

    ![image-20200813154201539](markdown/Spring项目单元测试.assets/image-20200813154201539.png)

    勾选需要测试的方法，点击OK，生成测试类。

    ![image-20200813154305895](markdown/Spring项目单元测试.assets/image-20200813154305895.png)

    ![image-20200813154419172](markdown/Spring项目单元测试.assets/image-20200813154419172.png)

2. 修改测试类

    ![image-20200813154646486](markdown/Spring项目单元测试.assets/image-20200813154646486.png)

    注意：需要添加依赖包

    ```yaml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
            </dependency>
    ```

    3. 添加断言

        ![image-20200813155435755](markdown/Spring项目单元测试.assets/image-20200813155435755.png)

        4. 运行单元测试

            ![image-20200813155531310](markdown/Spring项目单元测试.assets/image-20200813155531310.png)

            ![image-20200813155544362](markdown/Spring项目单元测试.assets/image-20200813155544362.png)
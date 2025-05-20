制作tomcat镜像部署web应用程序
引言
在当今的软件开发和部署领域，容器化技术凭借其高效、灵活和可移植的特性，成为了众多开发者的首选。其中，Docker 与 Tomcat 的组合，为 Web 应用的部署提供了便捷且可靠的解决方案。通过创建自定义的 Tomcat 镜像并部署 Web 应用，开发者能够快速搭建起稳定的应用环境，实现高效的开发与部署流程。本指南将详细介绍如何一步步制作自己的 Tomcat 镜像并部署 Web 应用程序，无论是新手还是有经验的开发者，都能从中获取清晰的操作指引，开启容器化部署 Web 应用的实践之旅。

准备工作
安装 Docker：确保你的系统上已经安装了 Docker。如果尚未安装，请根据你所使用的操作系统（如 Linux、Windows 或 MacOS），从 Docker 官方网站下载并安装对应的 Docker 版本。安装完成后，可在终端或命令提示符中输入docker --version命令，验证是否安装成功。若成功安装，会显示 Docker 的版本信息。
安装 JDK：由于 Tomcat 是基于 Java 运行的，需要在系统中安装 Java Development Kit（JDK）。建议安装与所选 Tomcat 镜像相匹配的 JDK 版本，本指南中使用的 Tomcat 镜像为tomcat:9.0-jdk11-corretto，因此需安装 JDK 11。可以从 Oracle 官方网站或其他可靠渠道下载并安装 JDK 11，安装完成后，配置好系统的JAVA_HOME环境变量，并将%JAVA_HOME%\bin（Windows 系统）或$JAVA_HOME/bin（Linux 和 MacOS 系统）添加到系统的PATH环境变量中，以便在终端中能够直接使用 Java 相关命令。在终端输入java -version命令，若能正确显示 Java 版本信息，则说明 JDK 安装和配置成功。
确保网络连接正常：在构建 Docker 镜像和运行容器的过程中，需要从网络下载基础镜像和相关依赖。因此，请确保你的设备已连接到网络，并且网络连接稳定，避免因网络问题导致操作失败。
1. 创建项目目录

mkdir tomcat-docker && cd tomcat-docker

2.准备 Web 应用
(1)创建示例 Web 应用目录结构

mkdir -p myapp/WEB-INF/classes

(2)创建一个简单的 JSP 文件

cat > myapp/index.jsp <<EOF

<%@ page Language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<title>Hello World</title>
</head>
<body>
<h1>Hello from Tomcat Docker!</h1>
<p>当前时间:<%= new java.util.Date() %></p>
</body>
</html>
EOF

(3) 创建 web.xml 配置文件

cat > myapp/WEB-INF/web.xml <<EOF

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
version="4.0">
</web-app>
EOF

(4)打包为 WAR 文件

jar cvf myapp.war -C myapp
3. 创建 Dockerfile

# 使用官方 Tomcat 基础镜像
FROM tomcat:9.0-jdk11-corretto
# 移除 Tomcat 默认应用（可选）
RUN rm -rf /usr/local/tomcat/webapps/*
# 复制 WAR 文件到 Tomcat 的 webapps 目录
COPY myapp.war /usr/local/tomcat/webapps/
# 可选：配置环境变量
ENV JAVA_OPTS="-Xms512m -Xmx1024m"
# 暴露 Tomcat 默认端口
EXPOSE 8080
# 启动 Tomcat
CMD ["catalina.sh", "run"]
4. 创建 .dockerignore 文件

git
*.log
5. 构建 Docker 镜像

docker build -t my-tomcat-app.
6. 运行 Docker 容器

docker run -d -p 8080:8080 --name my-tomcat-container my-tomcat-app


运行后验证：

docker ps
docker logs my-tomcat-container
7. 访问应用

localhost:8080/myapp/

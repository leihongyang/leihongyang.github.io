# 用户统一鉴权服务-适配器使用
```
官网文档：https://www.keycloak.org/documentation.html
adapter 文档：https://www.keycloak.org/docs/3.0/securing_apps/topics/oidc/java/spring-boot-adapter.html
```

### 1.环境准备
一个可以正常运行的spring boot的任意demo

### 2.创建REALM
Realm 类似于租户的概念，不同realm 中的用户完全隔离
realm里的用户可实现单点登录。一般realm在管理界面上创建，也可以用管理员API创建
在界面上手动创建一个demo的realm。

### 3.用户管理
公共代码：
```
Keycloak keycloak = KeycloakBuilder.builder()
  .serverUrl("http://111.202.198.105:28080/auth")
  .realm("master")
  .clientId("admin-cli")
  .username("kyland")
  .password("abc123kyland")
  .build();
RealmResource realmResource = keycloak.realm("demo");
UsersResource userRessource = realmResource.users();

//创建用户
// Define user
UserRepresentation user = new UserRepresentation();
user.setEnabled(true);
user.setUsername("tester1");
user.setFirstName("First");
user.setLastName("Last");
user.setEmail("tom+tester1@tdlabs.local");
user.setAttributes(Collections.singletonMap("origin", Arrays.asList("demo")));
// Create user (requires manage-users role)
userRessource.create(user);

//更新用户信息
UserResource user = userRessource.get(user_id);

//获取用户信息
userRessource.get(user_id)；

//删除用户
userRessource.delete(user_id)
```
### 4.角色管理
角色分两种，一种是app的角色，一种是realm的角色。
```
//公共代码：
RolesResource roleRessource = realmResource.roles();
//获取app角色
roleRessource.list()
//创建app角色
RoleRepresentation role = new RoleRepresentation();
role.setId();
role.setName();
...
roleRessource.create(role)
```
### 5.认证
```
Keycloak keycloak = KeycloakBuilder.builder()
  .serverUrl("http://111.202.198.105:28080/auth")
  .realm("master")
  .clientId("admin-cli")
  .username("kyland")
  .password("abc123kyland")
  .build();
```
### 6.客户端获取用户信息
```
//当前登录用户信息：
RefreshableKeycloakSecurityContext context = (RefreshableKeycloakSecurityContext)request.getAttribute("org.keycloak.KeycloakSecurityContext");

AccessToken token = context.getToken();
String sub = token.getSubject();//用户内码
System.out.println(sub);
String loginName = token.getPreferredUsername();//登录账号
System.out.println(loginName);

//Realm角色列表
AccessToken.Access access = token.getRealmAccess();
Set<String> roles = access.getRoles();
System.out.println(roles);
```
### 7.最后，附上POM文件内容
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.5.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<keycloak.version>4.2.0.Final</keycloak.version>
		<resteasy.version>3.1.4.Final</resteasy.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-freemarker</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.keycloak</groupId>
			<artifactId>keycloak-spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.jboss.resteasy</groupId>
			<artifactId>resteasy-jackson2-provider</artifactId>
			<version>${resteasy.version}</version>
		</dependency>
		<dependency>
			<groupId>org.jboss.resteasy</groupId>
			<artifactId>resteasy-client</artifactId>
			<version>${resteasy.version}</version>
		</dependency>
		<dependency>
			<groupId>org.keycloak</groupId>
			<artifactId>keycloak-admin-client</artifactId>
			<version>${keycloak.version}</version>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.keycloak.bom</groupId>
				<artifactId>keycloak-adapter-bom</artifactId>
				<version>${keycloak.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

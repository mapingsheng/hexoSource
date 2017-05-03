---
title: Maven忽略其他依赖jar包插件-maven-enforcer-plugin
date: 2016-09-20 10:54:31
categories: Maven
tags: Maven插件
---
我们在开发产品时，可能不同人开发不同的功能模块，比如：

•现在A开发a模块，B开发b模块

•a模块依赖modelman-1.0.0-SNAPSHOT.jar包；b模块依赖modelman-1.0.2-RELEASE.jar包；很显然目前最新的jar包版本应该都用modelman-1.0.2-RELEASE.jar这个版本

<!--more-->

•那么我们就可以把modelman-1.0.0-SNAPSHOT.jar这个jar包添加配置到下面中
	<exclude>com.maps:modelman:1.0.0-SNAPSHOT</exclude>

•添加完成后当编译产品时则会自动检测是否还有用到modelman:1.0.0-SNAPSHOT的模块，如检测出有，那么则中断编译并提示


其实我们只需在xml配置文件中添加插件maven-enforcer-plugin配置就行了

```java
	<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-enforcer-plugin</artifactId>
			<version>1.2</version>
			<executions>
				<execution>
					<id>enforce-banned-dependencies</id>
					<goals>
						<goal>enforce</goal>
					</goals>
					<configuration>
						<rules>
							<bannedDependencies>
								<searchTransitive>true</searchTransitive>
								<excludes>
									<!--在下面添加忽略的相关jar包 -->
									<exclude>com.maps:modelman:1.0.0-SNAPSHOT</exclude>
									<exclude>com.maps:dictionary:1.0.0-SNAPSHOT</exclude>
									<exclude>com.maps:flow</exclude>
								</excludes>
							</bannedDependencies>
						</rules>
						<fail>true</fail>
					</configuration>
				</execution>
			</executions>
	</plugin>
```

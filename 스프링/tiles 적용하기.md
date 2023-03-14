# 1. pom.xml에 tiles 라이브러리 추가 후 maven update
```xml
		<!-- The complete Tiles dependency with all supported technologies is achievable with the following dependency declaration: -->
		<dependency>
			<groupId>org.apache.tiles</groupId>
			<artifactId>tiles-extras</artifactId>
			<version>3.0.8</version>
		</dependency>

		<!-- The basic Tiles dependency with only servlet support can be added this way: -->
		<dependency>
			<groupId>org.apache.tiles</groupId>
			<artifactId>tiles-servlet</artifactId>
			<version>3.0.8</version>
		</dependency>

		<!-- If you need a dependency to Tiles JSP support, Declare the dependency this way: -->
		<dependency>
			<groupId>org.apache.tiles</groupId>
			<artifactId>tiles-jsp</artifactId>
			<version>3.0.8</version>
		</dependency>
```
# 2. servlet-context.xml에서 tiles 세팅
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans
	xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

	<!-- DispatcherServlet Context: defines this servlet's request-processing infrastructure -->

	<!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />

	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/**" location="/resources/" />

	<!-- tiles -->
	<beans:bean id="tilesConfigurer" class="org.springframework.web.servlet.view.tiles3.TilesConfigurer">
		<beans:property name="definitions">
			<beans:list>
				<beans:value>/WEB-INF/tiles/tiles-settings.xml</beans:value>
			</beans:list>
		</beans:property>
	</beans:bean>
	
	<beans:bean id="tilesViewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver">
		<beans:property name="viewClass" value="org.springframework.web.servlet.view.tiles3.TilesView" />
		<beans:property name="order" value="1" />
	</beans:bean>
	
	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/" />
		<beans:property name="suffix" value=".jsp" />
		<beans:property name="order" value="2" />
	</beans:bean>

	<context:component-scan	base-package="com.account_book.app" />
</beans:beans>
```
tiles 주석친 부분이 세팅한 부분인데 viewClass 부분에 매핑할 주소 안헷갈리게 적으면 된다.
# 3. tiles를 세팅할 디렉토리 생성
/WEB-INF/tiles
디렉토리는 연결만 잘시켜주면 되니까 안복잡해보이게 잘 정리하기
밑에 디렉토리 캡처해놓은 거 있음
# 4. tiles를 세팅할 xml 파일 생성
WEB-INF/tiles/tiles-settings.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tiles-definitions PUBLIC "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN" "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">

<tiles-definitions>
	<!-- defintion에서 화면의 기초가 될 template을 선택하고 해당 template에서 쓸 attribute를 추가한다. -->
	<definition name="default"	template="/WEB-INF/tiles/template.jsp">
		<put-attribute name="header" value="/WEB-INF/tiles/header.jsp" />
		<put-attribute name="footer" value="/WEB-INF/tiles/footer.jsp" />
	</definition>

	<!-- demfintion에서 servlet 경로(name)와 매칭시킬 view를 정한다. -->
	<definition name="*" extends="default">
		<put-attribute name="content" value="/WEB-INF/jsp/{1}.jsp" />
	</definition>
	
	<!-- 동적 mapping으로 * 하나에 와일드 카드 {1},{2}.. mapping된다. -->
	<!-- ?: 한글자, *: 아무 글자, [abc]: abc로 시작 -->
	<definition name="*/*" extends="default">
		<put-attribute name="content" value="/WEB-INF/jsp/{1}/{2}.jsp" />
	</definition>
	
	<definition name="*/*/*" extends="default">
		<put-attribute name="content" value="/WEB-INF/jsp/{1}/{2}/{3}.jsp" />
	</definition>
</tiles-definitions>
<put-attribute name="content">는 계속 바뀌어야 할 부분이다. 밑에 ~번에서 생성한 jsp 디렉토리 내에 있는 jsp 파일을 계속 갈아끼워 해당 영역의 콘텐츠를 동적으로 변경한다. 이 부분때매 몇시간동안 삽질했다.
![](https://velog.velcdn.com/images/wruoma/post/6c49326b-fd12-4e29-b1c2-118e4cda4a06/image.png)
```
# 4. 경로에 맞게 layout css 파일과 jsp 파일을 만들어준다.
header.jsp
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>

<div class="header">
	<a href="#">가계부 프로젝트</a>
	<div class="nav">
            <li><a href="#">공지사항</a>
            </li>
            <li><a href="#">가계부</a>
                <ui class="sub_menu">
                    <a href="#">설명서</a>
                    <a href="#">월별 가계부</a>
                    <a href="#">자산현황</a>
                </ui>
            </li>
            <li><a href="#">커뮤니티</a>
                <ui class="sub_menu">
                    <a href="#">자유 게시판</a>
                    <a href="#">질문 게시판</a>
                    <a href="#">건의 게시판</a>
                </ui>
            </li>
            <li><a href="#">간편 회원가입</a>
            </li>
            <li><a href="#">로그인</a>
            </li>
            </ul>
</div>
</div>
```
footer.jsp
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<div class="footer">footer</div>
template.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jstl/core_rt" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="description" content="">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
<link rel="stylesheet" href="css/layout/common.css" />
<link rel="stylesheet" href="css/layout/header.css" />
<link rel="stylesheet" href="css/layout/content.css" />
<link rel="stylesheet" href="css/layout/footer.css" />
<title>template</title>
</head>
<body>
<tiles:insertAttribute name="header" />
<div id="body"><tiles:insertAttribute name="content" /></div>
<tiles:insertAttribute name="footer" />
</body>
</html>
```
# 5. 레이아웃도 만들어준다.
common.css
```css
@charset "UTF-8";

@font-face {
	font-family: 'Pretendard-Regular';
	src: url('https://cdn.jsdelivr.net/gh/Project-Noonnu/noonfonts_2107@1.1/Pretendard-Regular.woff') format('woff');
	font-weight: 900;
	font-style: normal;
}

* {
	font-family: 'Pretendard-Regular';
	margin: 0px;
	padding: 0px;
}

body {
	width: 100%;
	height: 1000px;
}

a {
	text-decoration: none;
	color: black;
}
```
header.css
```css
@charset "UTF-8";

.header {
	box-sizing: border-box;
	display: flex;
	width: 100%;
	height: 50px;
	line-height: 50px;
	background-color: red;
}

.header>a {
	width: 150px;
	display: flex;
	justify-content: center;
	line-height: 50px;
}

.nav {
	box-sizing: border-box;
	width: 100%;
	display: flex;
	justify-content: space-around;
	list-style: none;
}

.nav>li {
	width: 100px;
	display: block;
	text-align: center;
	justify-content: center;
}

.nav>li>a {
	display: block;
}

.sub_menu {
	box-sizing: border-box;;
	display: block;
	width: 100px;
}

.sub_menu>a {
	box-sizing: border-box;
	display: flex;
	width: 100px;
	height: 30px;
	line-height: 30px;
	justify-content: center;
	background-color: white;
}
```
content.css
```css
@charset "UTF-8";

.content {
	box-sizing: border-box;
	display: flex;
	width: 100%;
	height: 850px;
	line-height: 850px;
	justify-content: center;
	font-size: 1.2rem;
	background-color: orange;
}
```
footer.css
```css
@charset "UTF-8";

.footer {
	box-sizing: border-box;
	display: flex;
	width: 100%;
	height: 100px;
	line-height: 100px;
	justify-content: center;
	font-size: 1.2rem;
	background-color: black;
	color: white;
}
```
# 6. 바꿔끼워 줄 페이지도 만든다.
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jstl/core_rt" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<!DOCTYPE html>
<html>
<head>
<title>home</title>
</head>
<body>
<div class="content">홈 콘텐츠</div>
</body>
</html>
```
7. 매핑시켜 줄 컨트롤러를 만든다.
```java
package com.account_book.app;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HomeController {
	@RequestMapping(value = "/")
	public String home() {
		return "home";
	}
}
```
여기서 return되는 home은 tiles-settings.xml의 '*'에 의해 매핑된다. home은 '*'의 value 값 내의 {1}에 해당된다.
![](https://velog.velcdn.com/images/wruoma/post/2d3dbc00-758c-4b94-ad37-2c2a018a39f9/image.png)

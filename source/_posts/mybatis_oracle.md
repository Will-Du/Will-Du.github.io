---
title: mybatis中oracle的批量新增与批量更新
date: 2019-12-05 19:16:53
tags: 数据库
---
&nbsp;&nbsp;&nbsp;&nbsp;mybatis中mysql的批量插入比较常规，但oracle的批量有所不同，若按照mysql的方式书写，则会报“命令为执行结束”的错误。所以一下列出oracle的批量新增与修改的几种方式。
```XML
<!-- 批量新增第一种方式 -->
<insert id="batchInsert" parameterType="java.util.List">
	INSERT INTO STUDENT(ID,NAME)
	<foreach collection="list" item="student" separator="UNION ALL">
		(
		SELECT 
			#{student.id},
			#{student.name}
		FROM
			dual
		)
	</foreach>
</insert>
```
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">注：表名之后没有VALUES。</label>
<!-- more -->
```XML
<!-- 批量插入第二种方式 -->
<insert id="batchInsert" parameterType="java.util.List">
	INSERT ALL
		<foreach collection="list" item="student">
			into STUDENT(ID,NAME)
			VALUES(#{student.id},#{student.name})
		</foreach>
	SELECT 1 FROM DUAL
</insert>
```
```XML
<!-- 批量更新 -->
<update id="batchUpdate" parameterType="java.util.List">
	<foreach collection="list" item="student" index="index" open="begin" close=";end;" separator=";">
		UPDATE STUDENT
		<set>
			<if test="student.name != null and student.name != ''">
				NAME = #{student.name}
			</if>
		</set>
		WHERE ID = #{student.id}
	</foreach>
</update>
```
```XML
<!-- 批量删除 -->
<delete id="batchDeleteByIds" parameterType="java.lang.String">
	DELETE FROM 
		STUDENT
	WHERE
		ID IN 
		<foreach collection="array" item="id" opean="(" separator="," close=")">
			#{id}
		</foreach>
</delete>
```
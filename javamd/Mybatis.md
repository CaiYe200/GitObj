# Mybatis

## Sql动态

##### if+where

```java
<select id="selectdata" resultType="data.Data">
    select id deid,name dename,sex desex,age deage from de1
    <where>
        <if test="deid!=null">
            id=#{deid}
        </if>
        <if test="dename!=null">
            and name=#{dename}
        </if>
        <if test="desex!=null">
            and sex=#{desex}
        </if>
        <if test="deage!=null">
            and age=#{deage}
        </if>
    </where>
</select>
```

##### set

```java
<update id="updatedata" >
    update de1
    <set>
        <if test="deid!=null">
            id=${deid}
        </if>
        <if test="dename!=null">
            ,name=${dename}
        </if>
        <if test="desex!=null">
            ,sex=${desex}
        </if>
        <if test="deage!=null">
            ,age=${deage}
        </if>
    </set>
    where id=#{deid}
</update>
```

##### choose when otherwise//选择（从上到下选择）

```java
<select id="selectbychoose" resultType="data.Data">
    <include refid="Selectbase"></include>
    where
    <choose>
        <when test="dename!=null">name=#{dename}</when>
        <when test="desex!=null">sex=#{desex}</when>
        <when test="deage!=null">age=#{deage}</when>
        <otherwise>1=1</otherwise>
    </choose>
</select>
```

#### foreach //批量操作

sql片段

```java
<mapper namespace="">
    <sql id="Selectbase">
        select id deid,name dename,sex desex,age deage from de1
    </sql>
    <select id="">
    	<include refid="Selectbase"></include>//sql片段
    <select/>
<mapper/>
```

批量查询

```java
<select id="selectbyforeach" resultType="data.Data">
    <include refid="Selectbase"></include>//sql片段
    where id in
    <foreach collection="array" item="idexmp" separator="," open="(" close=")">
        ${idexmp}
    </foreach>
</select>
```

批量增加

```java
<insert id="insertbyforeach">
    insert into de1(id,name,sex,age) values
    <foreach collection="list" item="insertdata" separator=",">
        (#{insertdata.deid},#{insertdata.dename},#{insertdata.desex},#{insertdata.deage})
    </foreach>
</insert>
```

批量修改

```java
<update id="updatebyforeach">
<foreach collection="list" separator=";" item="updatedata">
    update de1 set name =#{updatedata.dename},sex=#{updatedata.desex},
                   age=#{updatedata.deage} where id=#{updatedata.deid}
</foreach>
</update>
```

##### Mapper批量注册

配置文件里package

保证resource里有个跟mapper同路径的包

```java
    <mappers>
<!--        <mapper resource="DataMapper.xml"></mapper>-->
        <package name="mapper"/>//name为全路径名
    </mappers>
```

![image-20240309173227107](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240309173227107.png)

##### 分页插件page

导包

```java
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.2</version>
</dependency>
```

配置文件

```java
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>//shift+shift可查找原路径
</plugins>
```

test类

```java
PageHelper.startPage(1,3);//第一页，以3条数据分页；需放在查询语句之前
List<Data> dataList = dataMapper.selectAll();
for (Data data : dataList) {
    log.info(data.toString());
}
PageInfo<Data>pageInfo=new PageInfo<>();//PageInfo有多种方法，crtl+点击可查询
```
## MapStruct高性能实体类映射工具

### 1. 简介

> `MapStruct`是一个Java Annotation Processor注解处理器。利用注解生成类型安全的bean映射类。

### 2. 快速开始

> Talk is cheap.  Show me the code.

#### 2.1 类图设计

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//20200909000210.png)

#### 2.2 pom文件定义引入必要的依赖

设置项目属性

```xml
<properties>
    <!-- 设置项目源代码的编码方式 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <org.projectlombok.version>1.18.12</org.projectlombok.version>
    <org.mapstruct.version>1.3.1.Final</org.mapstruct.version>
</properties>
```

引入必要的依赖， 这里我们将`MapStruct`和`Lombok`结合来使用

```xml
<!-- lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${org.projectlombok.version}</version>
    <scope>provided</scope>
</dependency>

<!-- mapstruct核心依赖, 包含必须的注解类 -->
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>${org.mapstruct.version}</version>
</dependency>

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>

```

因为`MapStruct`是一种Java Annotation Processor， 所以必须在项目编译时处理对应的注解，需要对`maven-compilier-plugin`进行配置。

```xml
<plugins>
    <plugin>
        <!-- 注意, 因为子项目有编译期, 所以一定会自动引入父类中定义的compliper插件 -->
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <!-- 编译版本一定要3.5及以上版本 才会有 annotationProcessorPaths 属性 -->
        <!-- 对于低于3.5的版本 可以在 dependencies 块中添加依赖项，并设置 optional 属性-->
        <version>3.8.1</version>
        <configuration>
            <!-- 设置项目编译的版本 -->
            <source>1.8</source>
            <target>1.8</target>
            <!-- 设置编译过程中注解的处理器列表 -->
            <annotationProcessorPaths>
                <!-- 这里必须添加上lombok的编译期注释处理器, 在mapstruct-processor处理前完成类的处理 -->
                <path>
                    <groupId>org.projectlombok</groupId>
                    <artifactId>lombok</artifactId>
                    <version>${org.projectlombok.version}</version>
                </path>

                <!-- mapstruct-processor 包含注释处理器, 该注释处理器生成Mapper实现类-->
                <path>
                    <groupId>org.mapstruct</groupId>
                    <artifactId>mapstruct-processor</artifactId>
                    <version>${org.mapstruct.version}</version>
                </path>

            </annotationProcessorPaths>
        </configuration>
    </plugin>
</plugins>
```

#### 2.3 Car类

```java
package com.inconspicuousy.mapstruct.start.model;

import com.inconspicuousy.mapstruct.start.enumation.CarType;
import lombok.*;

/**
 * 汽车类
 * @author peng.yi
 */
@Data
public class Car {

    /** 生产商 */
    private String make;

    /** 汽车座位数 */
    private Integer numberOfSeats;

    /** 汽车类型 */
    private CarType type;

}
```

#### 2.4 CarType枚举类定义

```java
package com.inconspicuousy.mapstruct.start.enumation;

/**
 * 汽车类型枚举
 * @author peng.yi
 */
public enum CarType {

    SEDAN,
    SUV;

}

```

#### 2.5 CarDto

```java
package com.inconspicuousy.mapstruct.start.dto;

import lombok.Data;

/**
 * 汽车数据展示对象
 * @author peng.yi
 */
@Data
public class CarDto {

    /** 汽车生产商 */
    private String make;

    /** 座位数 */
    private Integer seatCount;

    /** 汽车类型 */
    private String type;

}

```

#### 2.6 核心CarMapper

```java
package com.inconspicuousy.mapstruct.start.mapper;

import com.inconspicuousy.mapstruct.start.dto.CarDto;
import com.inconspicuousy.mapstruct.start.model.Car;
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.factory.Mappers;

/**
 * Car 实体映射成 CarDto 工具类
 * @author peng.yi
 */
@Mapper
public interface CarMapper {

    // mapstruct主要做了两件事
    // 1、在编译期间根据注解生成对应的MapperImpl实现类
    // 2、在运行期间 Mappers.getMapper 找到对应的MapperImpl实现类
    CarMapper CAR_MAPPER = Mappers.getMapper(CarMapper.class);

    // 相同属性名， 相同数据类型， 不需要配置直接进行赋值
    // 相同属性名， 数据类型为枚举的话会自动转化成字符串型
    // 不同属性名需要借用Mapping注解进行转化
    @Mapping(target = "seatCount", source = "numberOfSeats")
    CarDto CarToCarDto(Car car);

}

```

#### 2.7 CarMapperTest测试类

```java
package com.inconspicuousy.mapstruct.start.mapper;
import com.inconspicuousy.mapstruct.start.dto.CarDto;
import com.inconspicuousy.mapstruct.start.enumation.CarType;

import com.inconspicuousy.mapstruct.start.model.Car;
import org.junit.Test;

import static org.junit.Assert.*;

/**
 * @author peng.yi
 */
public class CarMapperTest {

    @Test
    public void carToCarDto() {
        Car car = new Car();
        car.setMake("广汽");
        car.setNumberOfSeats(4);
        car.setType(CarType.SEDAN);

        CarDto carDto = CarMapper.CAR_MAPPER.CarToCarDto(car);
        System.out.println(carDto);
    }
}
```

#### 2.8 运行结果

```shell
CarDto(make=广汽, seatCount=4, type=SEDAN)

Process finished with exit code 0
```

### 3. 工具核心逻辑

- 在编译期间依赖注解生成对应的MapperImpl实现类，将转化的方法进行实现。

```java
package com.inconspicuousy.mapstruct.start.mapper;

import com.inconspicuousy.mapstruct.start.dto.CarDto;
import com.inconspicuousy.mapstruct.start.model.Car;
import javax.annotation.Generated;

@Generated(
    value = "org.mapstruct.ap.MappingProcessor",
    date = "2020-09-08T21:50:38+0800",
    comments = "version: 1.3.1.Final, compiler: javac, environment: Java 1.8.0_261 (Oracle Corporation)"
)
public class CarMapperImpl implements CarMapper {

    @Override
    public CarDto CarToCarDto(Car car) {
        if ( car == null ) {
            return null;
        }

        CarDto carDto = new CarDto();

        carDto.setSeatCount( car.getNumberOfSeats() );
        carDto.setMake( car.getMake() );
        if ( car.getType() != null ) {
            carDto.setType( car.getType().name() );
        }

        return carDto;
    }
}

```

- 在执行时，原Mapper接口中Mappers.getMapper方法实际上获取的就是Mapper实现类对象。

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//20200909001322.png)


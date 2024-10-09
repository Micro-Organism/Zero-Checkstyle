# Zero-Checkstyle-Boot
这是一个用于检查代码风格的Spring Boot项目。

# 1. 什么是checkstyle?
Checkstyle 是一个用于检查Java代码风格的工具，它可以帮助开发人员保持代码的一致性和可读性。Checkstyle 可以检查代码中的各种规则，例如缩进、空格、注释、命名约定等。
默认它支持google 和sun 的java style guide。而且它是高度可配置的，允许自定义编码规范，并可以对各种IDE（eclipse、Intellij）和构建工具(maven,gradle)的支持

# 2. 配置插件
在maven中一个名为maven-checkstyle-plugin的插件，用于执行CheckStyle task。下面是一个简单的配置。
在项目的pom.xml文件中添加以下依赖项：
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-checkstyle-plugin</artifactId>
            <version>3.5.0</version> <!-- Use the latest version here -->
            <configuration>
                <configLocation>/config/checkstyle.xml</configLocation>
                <encoding>UTF-8</encoding>
                <consoleOutput>true</consoleOutput>
                <failsOnError>true</failsOnError>
                <linkXRef>false</linkXRef>
            </configuration>
            <executions>
                <execution>
                    <id>check-style</id>
                    <phase>validate</phase>
                    <goals>
                        <goal>check</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <!-- ... other plugins ... -->
    </plugins>
</build>
```
我们定义了在maven lifecycle的validate阶段执行check task，并且如果发现有违反标准的情况就会fail当前的build。 maven-checkstyle-plugin内置了4种规范.

- config/sun_checks.xml
- config/maven_checks.xml
- config/turbine_checks.xml
- config/avalon_checks.xml

其中sun_checks.xml为默认值。如果想要使用其他三种规范，则只需配置configuration。下面是使用maven_checks.xml的示例。

# 3. 个性化配置
在项目的根目录下创建一个名为config的文件夹，并在其中创建一个名为checkstyle.xml的文件。
该文件将包含您的自定义CheckStyle规则。
我们可以使用默认的规范未见，当然也支持自定义。下面是google的一个checkstyle规范文件: 
```xml
<?xml version="1.0"?>
<!DOCTYPE module PUBLIC
        "-//Puppy Crawl//DTD Check Configuration 1.3//EN"
        "http://www.puppycrawl.com/dtds/configuration_1_3.dtd">
<module name="Checker">
    <!-- 检查文件是否以一个空行结束 -->
    <module name="NewlineAtEndOfFile"/>
    <!-- 文件长度不超过1500行 -->
    <module name="FileLength">
        <property name="max" value="1500"/>
    </module>
    <!-- 修复Checkstyle version版本大于8.23报错：TreeWalker is not allowed as a parent of LineLength 问题，移动LineLength到这里解决 -->
    <!-- 每行不超过140个字符 -->
    <module name="LineLength">
        <property name="max" value="140" />
    </module>
    <!-- 每个java文件一个语法树 -->
    <module name="TreeWalker">
        <!-- import检查-->
        <!-- 检查是否从非法的包中导入了类 -->
        <module name="IllegalImport"/>
        <!-- 检查是否导入了多余的包 -->
        <module name="RedundantImport"/>
        <!-- 没用的import检查，比如：1.没有被用到2.重复的3.import java.lang的4.import 与该类在同一个package的 -->
        <module name="UnusedImports" />
        <!-- 注释检查 -->
        <!-- 检查构造函数的javadoc -->
        <module name="JavadocType">
            <property name="allowUnknownTags" value="true"/>
            <message key="javadoc.missing" value="类注释：缺少Javadoc注释。"/>
        </module>
        <!-- 命名检查 -->
        <!-- 局部的final变量，包括catch中的参数的检查 -->
        <module name="LocalFinalVariableName" />
        <!-- 局部的非final型的变量，包括catch中的参数的检查 -->
        <module name="LocalVariableName" />
        <!-- 包名的检查（只允许小写字母），默认^[a-z]+(\.[a-zA-Z_][a-zA-Z_0-9_]*)*$ -->
        <module name="PackageName">
            <property name="format" value="^[a-z]+(\.[a-z][a-z0-9]*)*$" />
            <message key="name.invalidPattern" value="包名 ''{0}'' 要符合 ''{1}''格式."/>
        </module>
        <!-- 仅仅是static型的变量（不包括static final型）的检查 -->
        <module name="StaticVariableName" />
        <!-- Class或Interface名检查，默认^[A-Z][a-zA-Z0-9]*$-->
        <module name="TypeName">
            <property name="severity" value="warning"/>
            <message key="name.invalidPattern" value="名称 ''{0}'' 要符合 ''{1}''格式."/>
        </module>
        <!-- 非static型变量的检查 -->
        <module name="MemberName" />
        <!-- 方法名的检查 -->
        <module name="MethodName" />
        <!-- 方法的参数名 -->
        <module name="ParameterName " />
        <!-- 常量名的检查（只允许大写），默认^[A-Z][A-Z0-9]*(_[A-Z0-9]+)*$ -->
        <module name="ConstantName" />
        <!-- 定义检查 -->
        <!-- 检查数组类型定义的样式 -->
        <module name="ArrayTypeStyle"/>
        <!-- 检查long型定义是否有大写的“L” -->
        <module name="UpperEll"/>
        <!-- 长度检查 -->

        <!-- 方法不超过50行 -->
        <module name="MethodLength">
            <property name="tokens" value="METHOD_DEF" />
            <property name="max" value="50" />
        </module>
        <!-- 方法的参数个数不超过5个。 并且不对构造方法进行检查-->
        <module name="ParameterNumber">
            <property name="max" value="5" />
            <property name="ignoreOverriddenMethods" value="true"/>
            <property name="tokens" value="METHOD_DEF" />
        </module>
        <!-- 空格检查-->
        <!-- 方法名后跟左圆括号"(" -->
        <module name="MethodParamPad" />
        <!-- 在类型转换时，不允许左圆括号右边有空格，也不允许与右圆括号左边有空格 -->
        <module name="TypecastParenPad" />
        <!-- 检查在某个特定关键字之后应保留空格 -->
        <module name="NoWhitespaceAfter"/>
        <!-- 检查在某个特定关键字之前应保留空格 -->
        <module name="NoWhitespaceBefore"/>
        <!-- 操作符换行策略检查 -->
        <module name="OperatorWrap"/>
        <!-- 圆括号空白 -->
        <module name="ParenPad"/>
        <!-- 检查分隔符是否在空白之后 -->
        <module name="WhitespaceAfter"/>
        <!-- 检查分隔符周围是否有空白 -->
        <module name="WhitespaceAround"/>
        <!-- 修饰符检查 -->
        <!-- 检查修饰符的顺序是否遵照java语言规范，默认public、protected、private、abstract、static、final、transient、volatile、synchronized、native、strictfp -->
        <module name="ModifierOrder"/>
        <!-- 检查接口和annotation中是否有多余修饰符，如接口方法不必使用public -->
        <module name="RedundantModifier"/>
        <!-- 代码块检查 -->
        <!-- 检查是否有嵌套代码块 -->
        <module name="AvoidNestedBlocks"/>
        <!-- 检查是否有空代码块 -->
        <module name="EmptyBlock"/>
        <!-- 检查左大括号位置 -->
        <module name="LeftCurly"/>
        <!-- 检查代码块是否缺失{} -->
        <module name="NeedBraces"/>
        <!-- 检查右大括号位置 -->
        <module name="RightCurly"/>
        <!-- 代码检查 -->
        <!-- 检查空的代码段 -->
        <module name="EmptyStatement"/>
        <!-- 检查在重写了equals方法后是否重写了hashCode方法 -->
        <module name="EqualsHashCode"/>
        <!-- 检查局部变量或参数是否隐藏了类中的变量 -->
        <module name="HiddenField">
            <property name="tokens" value="VARIABLE_DEF"/>
        </module>
        <!-- 检查子表达式中是否有赋值操作 -->
        <module name="InnerAssignment"/>
        <!-- 检查switch语句是否有default -->
        <module name="MissingSwitchDefault"/>
        <!-- 检查是否有过度复杂的布尔表达式 -->
        <module name="SimplifyBooleanExpression"/>
        <!-- 检查是否有过于复杂的布尔返回代码段 -->
        <module name="SimplifyBooleanReturn"/>
        <!-- 类设计检查 -->
        <!-- 检查类是否为扩展设计l -->
        <!-- 检查只有private构造函数的类是否声明为final -->
        <module name="FinalClass"/>
        <!-- 检查接口是否仅定义类型 -->
        <module name="InterfaceIsType"/>
        <!-- 检查类成员的可见度 检查类成员的可见性。只有static final 成员是public的
        除非在本检查的protectedAllowed和packagedAllowed属性中进行了设置-->
        <module name="VisibilityModifier">
            <property name="packageAllowed" value="true"/>
            <property name="protectedAllowed" value="true"/>
        </module>
        <!-- 语法 -->
        <!-- String的比较不能用!= 和 == -->
        <module name="StringLiteralEquality"/>
        <!-- 限制for循环最多嵌套2层 -->
        <module name="NestedForDepth">
            <property name="max" value="2"/>
        </module>
        <!-- if最多嵌套3层 -->
        <module name="NestedIfDepth">
            <property name="max" value="3"/>
        </module>
        <!-- 检查未被注释的main方法,排除以Appllication结尾命名的类 -->
        <module name="UncommentedMain">
            <property name="excludedClasses" value=".*[Application,Test]$"/>
        </module>
        <!-- 禁止使用System.out.println -->
        <module name="Regexp">
            <property name="format" value="System\.out\.println"/>
            <property name="illegalPattern" value="true"/>
        </module>
        <!-- return个数 3个-->
        <module name="ReturnCount">
            <property name="max" value="3"/>
        </module>
        <!--try catch 异常处理数量 3-->
        <module name="NestedTryDepth ">
            <property name="max" value="3"/>
        </module>
        <!-- clone方法必须调用了super.clone() -->
        <module name="SuperClone" />
        <!-- finalize 必须调用了super.finalize() -->
        <module name="SuperFinalize" />
    </module>
</module>
```

# 4. 跳过对指定文件的某些检查
如果对于指定文件检查出了一些问题，但是你想忽略它，则可以使用suppression。 例如在上述例子中有一个checkstyle error是说某行超过了100字符。
如果我们不想修复这个错误怎么办那？可以将其suppress掉。 方法是建立一个checkstyle-suppressions.xml文件。其中加入下述内容
```xml
<?xml version="1.0"?>

<!DOCTYPE suppressions PUBLIC
        "-//Puppy Crawl//DTD Suppressions 1.0//EN"
        "http://www.puppycrawl.com/dtds/suppressions_1_0.dtd">

<suppressions>
    <suppress checks="NewlineAtEndOfFileCheck"
              files="HelloWorldController.java"
    />
</suppressions>
```
然后配置maven-checkstyle-plugin设置suppressionsLocation。
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-checkstyle-plugin</artifactId>
            <version>3.5.0</version> <!-- Use the latest version here -->
            <configuration>
                <configLocation>${basedir}/config/maven_checks.xml</configLocation>
                <suppressionsLocation>${basedir}/config/checkstyle-suppressions.xml</suppressionsLocation>
                <encoding>UTF-8</encoding>
                <consoleOutput>true</consoleOutput>
                <failsOnError>true</failsOnError>
                <linkXRef>false</linkXRef>
            </configuration>
            <executions>
                <execution>
                    <id>check-style</id>
                    <phase>validate</phase>
                    <goals>
                        <goal>check</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <!-- ... other plugins ... -->
    </plugins>
</build>
```
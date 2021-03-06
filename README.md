# clyoudu-util

## byteformat
Formatting byte size to human readable format, or converting unit from X to Y.
- unit system: `SI/IEC`
- scale: number of decimal places
- roundingMode: round policy
- displayUnitName: display unit name if true, or display value only
Usage:
```java
System.out.println(ByteUnitFormat.B.to(ByteUnitFormat.K, 1024L, Rate.SI));//1.02K
System.out.println(ByteUnitFormat.B.to(ByteUnitFormat.K, 1024L, Rate.IEC));//1K
System.out.println(ByteUnitFormat.M.to(ByteUnitFormat.G, 4096L, Rate.IEC));//4G
System.out.println(ByteUnitFormat.M.to(ByteUnitFormat.G, 4096L));//4G
System.out.println(ByteUnitFormat.B.humanReadable(174541987L));//166.5M
System.out.println(M.humanReadable(1024L));//1G
System.out.println(M.humanReadable(1024L, false));//1
```

## consoletable
Printing formatted table to console(simple ascii table).
- lineSep: separator of each line
- verticalSep: vertical separator of each cell(one character)
- horizontalSep: horizontal separator of each cell(one character)
- joinSep: cell point(one character)
- nullPolicy: policy for null value process
- restrict: number of columns for each row/header must be the same when strict mode used
Usage:
```java
List<Cell> header = new ArrayList<Cell>(){{
    add(new Cell("name"));
    add(new Cell("email"));
    add(new Cell("tel"));
}};
List<List<Cell>> body = new ArrayList<List<Cell>>(){{
    add(new ArrayList<Cell>(){{
        add(new Cell("kat"));
        add(new Cell(Align.CENTER,"kat@gimal.com"));
        add(new Cell(Align.RIGHT,"54321"));
    }});
    add(new ArrayList<Cell>(){{
        add(new Cell("ashe"));
        add(new Cell("ashe_111@hotmail.com"));
        add(new Cell("9876543210"));
    }});
    add(new ArrayList<Cell>(){{
        add(null);
        add(new Cell(null));
        add(new Cell(Align.LEFT,"11"));
    }});
}};
//default
new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).build().print();

//no header
new ConsoleTable.ConsoleTableBuilder().addRows(body).build().print();

//restrict
header.add(new Cell("not restrict"));
//new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).restrict(false).build().print();
new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).restrict(true).build().print();

//"null"
new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).nullPolicy(NullPolicy.NULL_STRING).build().print();
new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).nullPolicy(NullPolicy.THROW).build().print();

//line sep
//new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).lineSep("\n\n").build().print();

//vertical sep & horizontal sep & join sep
//new ConsoleTable.ConsoleTableBuilder().addHeaders(header).addRows(body).verticalSep("*").horizontalSep("*").joinSep("*").build().print();

/**
 * +------+----------------------+------------+
   | name | email                | tel        |
   +------+----------------------+------------+
   | kat  |    kat@gimal.com     |      54321 |
   | ashe | ashe_111@hotmail.com | 9876543210 |
   |      |                      | 11         |
   +------+----------------------+------------+
 */
```

more similar tools:

[TableList.java](https://github.com/therealfarfetchd/crogamp/blob/master/src/com/github/mrebhan/crogamp/cli/TableList.java)

[java-ascii-table](http://bethecoder.com/applications/products/asciiTable.action)

[asciitable](https://github.com/vdmeer/asciitable)

## pakagegenerator
generate directory structure to string
- ignoreDirs: ignored directories will not appended to result
- packageCollapse: if true, empty directories in a row, like 'github/clyoudu/util', will generated as `github.clyoudu.util`
- generateFile: if true, files will appended to result
Usage:
```java
System.out.print(generate(System.getProperty("user.dir") + "/src/main/java", new HashSet<>(Arrays.asList(".idea", "target", ".git")), true, true));
System.out.println("\n\n------------------------------------------------\n");
System.out.print(generate(System.getProperty("user.dir") + "/src/main/java", new HashSet<>(Arrays.asList(".idea", "target", ".git")), false, false));
/**
 * java.github.clyoudu
   ├── byteformat
   │   └── ByteUnitFormat.java
   ├── consoletable
   │   ├── ConsoleTable.java
   │   ├── enums
   │   │   ├── Align.java
   │   │   └── NullPolicy.java
   │   ├── table
   │   │   ├── Body.java
   │   │   ├── Cell.java
   │   │   └── Header.java
   │   └── util
   │       └── PrintUtil.java
   └── packagegenerator
       └── PackageGenerator.java

   ------------------------------------------------

   java
   └── github
       └── clyoudu
           ├── byteformat
           ├── consoletable
           │   ├── enums
           │   ├── table
           │   └── util
           └── packagegenerator
 */
```

## collectorext
some extend methods for `java.util.stream.Collectors`
- `toJsonArray`: collect array as `com.alibaba.fastjson.JSONArray`
    ```java
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("a", "a");
        map.put("b", "b");
        map.put("c", "c");
        map.put("d", "d");
        System.out.println(map.entrySet().stream().collect(toJsonArray()));
        System.out.println(Stream.of("a","b","c","d").collect(toJsonArray()));
        System.out.println(Stream.of("a","b","c","d").map(s -> new JSONObject().fluentPut(s,s)).collect(toJsonArray()));
    }
    ```

## proxysqlclient
connect proxysql admin via java code, using [jasync-sql](https://github.com/jasync-sql/jasync-sql)
- proxysql & mysql supported
- transaction supported
- batch execute supported
```java
    public static void main(String[] args) {
        //select
        log.info(JSON.toJSONString(executeQuery("localhost", 6032, "main", "root", "root",
                "select * from mysql_users",
                null)));
        //ddl & batch execute
        log.info(JSON.toJSONString(execute("localhost", 6032, "main", "root", "root",
                Arrays.asList("INSERT INTO mysql_users(username,password,default_hostgroup) VALUES ('app','pass',2)", "load mysql user to runtime", "save mysql user to disk"),
                null, false)));
    }
```

## fileutil
read/save file(classpath or file system) using spring Resource.
- jar or docker supported.
- more elegant way

```java
package github.clyoudu.fileutil;

import lombok.extern.slf4j.Slf4j;

/**
 * @author leichen
 * @date 2019/12/2 5:11 下午
 */
@Slf4j
public class FileUtilTest {

    public static void main(String[] args) {
        log.info("{}", "test".equals(FileUtil.readClasspathFile("/test.txt")));
        log.info("{}", "test".equals(FileUtil.readOsFile( System.getProperty("user.dir") + "/src/main/resources/test.txt")));
    }

}
```

## randompassword
generate random password

```java
package github.clyoudu.randompassword;

import lombok.extern.slf4j.Slf4j;

/**
 * @author leichen
 * @date 2019/12/2 5:57 下午
 */
@Slf4j
public class RandomPasswordUtilTest {

    public static void main(String[] args) {
        log.info("{}", RandomPasswordUtil.randomPassword(16, 2, 2, 2, 2));//QV6~@9oEHR7umzOr
        log.info("{}", RandomPasswordUtil.randomPassword(16, 2, 2, 2, 2));//R2^8egH3QF%OUg~P
        log.info("{}", RandomPasswordUtil.defaultModelRandomPassword());//f4&8hDebq$&vTV4o
        log.info("{}", RandomPasswordUtil.defaultModelRandomPassword());//QSPUaq9j3~eHw8mK
        log.info("{}", RandomPasswordUtil.randomPassword(8, 1, 1, 1, 1));//2e%v#U0D
        log.info("{}", RandomPasswordUtil.randomPassword(8, 1, 1, 1, 1));//nF*8Cv5b
        log.info("{}", RandomPasswordUtil.randomPassword(8, 1, 1, 0, 1));//22IieW9V
        log.info("{}", RandomPasswordUtil.randomPassword(8, 1, 1, 0, 1));//UVE1SxLP
    }

}
```
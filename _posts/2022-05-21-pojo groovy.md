---

layout: post
title: "pojo"
date: 2022-05-21 10:25:06 +0800
comments: true
category: mysql
tag: [mysql, java]

---

# Pojo groovy

代码仓库:  [https://github.com/gongchangyou/tool](https://github.com/gongchangyou/tool)

自动生成pojo

```
import com.intellij.database.model.DasTable
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */

typeMapping = [
  (~/(?i)int/)                      : "Long",
  (~/(?i)float|double|decimal|real/): "Double",
  (~/(?i)datetime|timestamp/)       : "java.sql.Timestamp",
  (~/(?i)date/)                     : "java.sql.Date",
  (~/(?i)time/)                     : "java.sql.Time",
  (~/(?i)/)                         : "String"
]

FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
  SELECTION.filter { it instanceof DasTable }.each { generate(it, dir) }
}

def generate(table, dir) {
  def className = javaName(table.getName(), true)
  def fields = calcFields(table)
  def packageName = getPackageName(dir)
  new File(dir, className + ".java").withPrintWriter { out -> generate(out, className, fields, packageName, table) }
}

def generate(out, className, fields, packageName, table) {
  out.println "package $packageName"
  out.println ""
  out.println ""
  out.println "import com.baomidou.mybatisplus.annotation.IdType;\n" +
          "import com.baomidou.mybatisplus.annotation.TableId;\n" +
          "import com.baomidou.mybatisplus.annotation.TableName;\n" +
          "import lombok.AllArgsConstructor;\n" +
          "import lombok.Builder;\n" +
          "import lombok.Data;\n" +
          "import lombok.NoArgsConstructor;"
    out.println ""
    out.println "@Builder\n" +
            "@AllArgsConstructor\n" +
            "@NoArgsConstructor\n" +
            "@Data\n" +
            "@TableName(\"${table.getName()}\")"
    out.println "public class $className {"
  out.println ""
  fields.each() {
    if (it.comment != "") {
        out.println "  /**"
        out.println "   * ${it.comment}"
        out.println "   */"
    }
      if (it.isPrimaryKey) {
          out.println "  @TableId(type = IdType.AUTO)"
      }
    out.println "  private ${it.type} ${it.name};"
  }
  out.println ""
//  fields.each() {
//    out.println ""
//    out.println "  public ${it.type} get${it.name.capitalize()}() {"
//    out.println "    return ${it.name};"
//    out.println "  }"
//    out.println ""
//    out.println "  public void set${it.name.capitalize()}(${it.type} ${it.name}) {"
//    out.println "    this.${it.name} = ${it.name};"
//    out.println "  }"
//    out.println ""
//  }
  out.println "}"
}

def calcFields(table) {
    DasUtil.getColumns(table).reduce([]) { fields, col ->
    def spec = Case.LOWER.apply(col.getDataType().getSpecification())
    def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
    fields += [[
                 name : javaName(col.getName(), false),
                 type : typeStr,
                 comment : col.getComment(),
                 isPrimaryKey :  DasUtil.isPrimary(col),
                 annos: ""]]
  }
}

def javaName(str, capitalize) {
  def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
    .collect { Case.LOWER.apply(it).capitalize() }
    .join("")
    .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
  capitalize || s.length() == 1? s : Case.LOWER.apply(s[0]) + s[1..-1]
}

def getPackageName(dir) {
    return dir.toString().replaceAll("\\\\", ".").replaceAll("/", ".").replaceAll("^.*src(\\.main\\.java\\.)?", "") + ";"
}

```


# JSON
JSON Schema สำหรับ component แบ่งเป็น 3 ส่วน
- component: ระบุชื่อ component ว่าเป็น component ใด
- props: ระบุ props ต่างๆ ที่ต้องการส่งให้ component
- \_meta: เป็นส่วนที่ใช้เสริมความสามารถต่างๆของ component นั้นๆ
```js
{
  "_meta": {
    "translate": {
      "content": "submit-button"
    }
  },
  "component": "Button",
  "size": "large",
  "primary": true
}
```

## TOC
* [\_meta](#_meta)
  * [hoc/hocs](#hochocs)
  * [context](#context)
  * [translate](#translate)
  * [slots](#slots)
  * [style](#style)
  * [hiddenOn](#hiddenon)
  * [useTheme](#usetheme)
  * [cautions](#cautions)

## \_meta

### hoc/hocs
สามารถใส่ HOCs ให้ component เพื่อให้ทำงานตาม logic ต่างๆได้ โดยใส่ได้ 2 รูปแบบ คือ hoc ซึ่งรับ string และ hocs ซึ่งรับ array(string)
> ```js
> "hoc": <hoc-name>
> "hocs": [<hoc-name>, <hoc-name>, ...]
> ```
```js
// hoc example
"_meta": {
  "hoc": "withCartSpinner"
}
// hocs example
"_meta": {
  "hocs": ["withCartSpinner", "withLoader"]
}
```
**ข้อควรระวัง** [การใช้งาน ```\_meta: hoc``` คู่กับ ```\_meta: contexts```](#cautions)

### context
คือการ apply HOCs withContextConsumer ให้กับ components โดย HOC นี้จะทำหน้าที่ map context ที่ได้รับเป็น props ให้กับ component
> ```js
> "context": {
>   <component-props>: <get-context-key>,
>   ...
> }
> ```
```js
// example
"_meta": {
  "context": {
    "loading": "formLoading"
  }
}
```
**ข้อควรระวัง** [การใช้งาน ```\_meta: hoc``` คู่กับ ```\_meta: contexts```](#cautions)

### translate

```js
"_meta": {
  "translate": {
    "validationErrors": {
      "isEmail": "validate-email-wrong-format"
    },
    "content": "email-input-label"
  }
}
```
### slots
```js
"_meta": {
  "slots": {
    "trigger": {
      "component": "Button",
      "content": "Open Modal"
    }
  }
}
```
### style
```js
"_meta": {
  "style": {
    "computer": {
      "background": "red"
    },
    "tablet": {
      "paddingLeft": "23px"
    },
    "mobile": {
      "position": "absolute !important",
      "bottom": 0
    }
  }
}
```
### hiddenOn
```js
{
  "_meta": {
    "hiddenOn": ["mobile", "tablet"]
  },
  "component": "PaginationBar"
},
{
  "_meta": {
    "hiddenOn": ["computer"]
  },
  "component": "PaginationDropdown"
}
```
### useTheme
```
  "_meta": {
    "useTheme": false
  }
```
### cautions
ควรระวังในการใช้ context คู่กับ hocs เนื่องจากอาจทำให้มีปัญหาเรื่องของ flow ของ props
> เมื่อใช้ context กับ hocs คู่กันจะเกิด React Virtual Dom ในรูปแบบ
> ```js
> <ContextConsumer>           // \_meta: context
>   <HOCs>                    // \_meta: hoc
>     <ComposedComponent />   // component
>   </HOCs>
> </ContextConsumer>
> ```

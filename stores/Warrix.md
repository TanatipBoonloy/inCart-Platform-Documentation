#Store Information

## Table of Contents
* [Font Styles](#font-styles)
* [Theme Colors](#theme-colors)
* [How to Use](#how-to-use)

## Font Styles

ฟ้อนท์ที่ใช้ใน warrix มี 4 รูปแบบ EN (Norwester, RobotoCondensed), TH (DBHelvethaicaX, DBThaiTextX)


1. Norwester (Header EN)
```scss
    font-family: Norwester;
    
    // available font-weight
    //  400
```

2. Roboto Condensed (Body EN)
```scss
    font-family: RobotoCondensed;
    
    // available font-weight
    //  300
    //  400
    //  700
```

3. DB Helvethaica X (Header TH)
```scss
    font-family: DBHelvethaicaX;
        
    // available font-weight
    //  100
    //  200
    //  300
    //  400
    //  500
    //  700
    //  900
```

3. DB ThaiText X (Body TH)
```scss
    font-family: DBThaiTextX;
        
    // available font-weight
    //  300
    //  400
    //  700
```

## Theme Colors
```scss
    /* primary color (black) */
        color: #000000;
    
    /* secondary color (orange) */
        color: #d35b13;
        
    /* dark grey */
        color: #1b1b1b;
            
    /* grey 1 */
        color: #787878;
    
    /* grey 2 */
        color: #919191;
    
    /* grey 3 */
        color: #999999;
    
    /* light grey 1 */
        color: #e4e4e4;
    
    /* light grey 2 */
        color: #dadada;
    
    /* light grey 3 */
        color: #efefef;
        
    /* white */
        color: #ffffff;
        
    /* ICON OTHER (yellow) */
        color: #ffffff;
        
    /* success (green) */
        color: #33b824;
        
    /* warning (red) */
        color: #d21c00;
       
```

## How to Use

การใส่ค่า font-family ต้องคำนึงถึง tag และจุดมุ่งหมายที่จะนำไปใช้ว่าเราต้องการให้เป็น header หรือ body

```text
    h1 - h6 = header
    p, a, span, ... = body
```

ทุกครั้งที่ใส่ font-family ต้องทำให้ support ทั้งสองภาษา เช่น

```scss
    /* Header */
    font-family: Norwester, DBHelvethaicaX;
    
    /* Body */
    font-family: DBThaiTextX, RobotoCondensed;
```

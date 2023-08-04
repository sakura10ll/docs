### 在居中元素定宽定高的情况下

```html
<style>
.wp{
    border: 1px solid red;
    width: 300px;
    height: 300px;
}

.box{
    background: green;
    width: 100px;
    height: 100px;
}

</style>

<body>
    <div class="wp">
        <div class="box"></div>
    </div>
</body>
```


##### 1. `absolute` + `margin`
```css
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: calc(100px - 50%);
    margin-left: calc(100px - 50%);
}
```

##### 2. `absolute` + `margin` `auto`
```css
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
}
```

##### 3. `absolute` + `calc` 与第一种类似
```css
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: calc(50% - 100px);
    left: calc(50% - 100px);
}
```

- - - 
### 居中元素不定宽高
```html
<style>
.wp{
    border: 1px solid red;
    width: 300px;
    height: 300px;
}

.box{
    background: green;
}

</style>

<body>
    <div class="wp">
        <div class="box">text</div>
    </div>
</body>
```
##### 1. `absolute` + `transform`
```css
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

##### 2. `lineHeight`
```css
.wp{
    line-height: 300px;
    text-align: center;
}
.box{
    display: inline-block;
    vertical-align: middle;
    line-height: initial;
}
```

##### 3. `css-table`
```css
.wp{
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
.box{
    display: inline-block;
}

```

##### 4. `flex`
```css
.wp{
    display: flex;
    justify-content: center;
    align-items: center;
}
```

##### 5. `grid`
```css
.wp{
    display: grid;
}
.box{
    align-self: center;
    justify-self: center;
}
```
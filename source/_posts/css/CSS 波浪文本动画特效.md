# 波浪文本动画特效

>通过 `animation` 改变高度实现动画效果

## index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link href="./style.css" rel="stylesheet">
  <title>CSS 波浪文本动画特效</title>
  <link>
</head>
<body>
  <div class="wavy">
    <span style="--i:1">内</span>
    <span style="--i:2">容</span>
    <span style="--i:3">加</span>
    <span style="--i:4">载</span>
    <span style="--i:5">中</span>
    <span style="--i:6">.</span>
    <span style="--i:7">.</span>
    <span style="--i:8">.</span>
  </div>
</body>
</html>
```

## style.css

```css
*{
  margin: 0;
  padding: 0;
  font-family: "微软雅黑"
}

body{
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background: #000;
}

.wavy{
  position: relative;
  -webkit-box-reflect: below -12px linear-gradient(transparent, rgba(0, 0, 0, 0.2))
}

.wavy span{
  position: relative;
  display: inline-block;
  color: #fff;
  font-size: 2em;
  animation: animate 1s ease-in-out infinite;
  animation-delay: calc(.1s*var(--i))
}

@keyframes animate{
  0%{
    transform: translateY(0px)
  }
  20%{
    transform: translateY(-24px)
  }
  40%, 100%{
    transform: translateY(0px)
  }
}
```

**tips**:

1. `box-reflect`
2. `CSS3` 的 `var` 使用

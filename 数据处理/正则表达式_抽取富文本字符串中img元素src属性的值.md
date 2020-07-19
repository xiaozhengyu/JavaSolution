# 抽取富文本字符串中<img>元素src属性的值

---

```java
public class Test {

    /**
     * 抽取包含在富文本中的所有图片地址
     *
     * @param questionContent - 富文本
     */
    private static Set<String> searchImgAddressInTheText(String questionContent) {
        java.util.Set<String> imgAddressSet = new HashSet<>();

        System.out.println("接收到的字符串：\n" + questionContent + "\n");

        // 1.反转义html
        StringEscapeUtils.unescapeHtml(questionContent);
        System.out.println("处理后的字符串：\n" + questionContent + "\n");

        // 2.匹配img元素
        String imgElementRegularExpression = "<(img|IMG)(.*?)(>|/>|></img>)";
        String srcPropertyExpression = "(src|SRC)=([\"'])(.*?)([\"'])";
        Pattern imgElementPattern = Pattern.compile(imgElementRegularExpression);
        Matcher imgElementMatcher = imgElementPattern.matcher(questionContent);
        while (imgElementMatcher.find()) {
            // 获取匹配到的img元素中的内容
            String imgElementString = imgElementMatcher.group(2);

            // 3.匹配img元素中的src属性
            Pattern srcPropertyPattern = Pattern.compile(srcPropertyExpression);
            Matcher srcPropertyMatcher = srcPropertyPattern.matcher(imgElementString);
            if (srcPropertyMatcher.find()) {
                String srcString = srcPropertyMatcher.group(3);
                System.out.println(srcString);
            }
        }

        return imgAddressSet;
    }

    public static void main(String[] args) {
        String questionContent = "<p><img src=\"https://img-blog.csdg.cn/aaaaaaaaaaaaa\"></p>" +
                "<p><img src=\"https://img-blog.csdg.cn/bbbbbbbbbbbbb\"></p>" +
                "<p><img src=\"https://img-blog.csdg.cn/ccccccccccccc\"></p>";
        Test.searchImgAddressInTheText(questionContent);
    }
}
```
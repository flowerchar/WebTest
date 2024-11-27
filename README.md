1. 为什么车票查询是五个，因为这是边界值，比如时间区间是[5,12]，那么5之前，5,5-12中间任意，12,12以后，这样就是五个
2. 哪怕是学生票高铁票的前置条件，这两项也必须是未选中的状态，因为这是在做这项事之前的准备工作
3. 因为可能一个站点有很多的选择，因此在这里车票需要加上回车键确定，这样才能保证输入的正确性。并且在测试需求文档中测试范围内写明了**输入框键入输入值并回车的方式**
4. 测试需求文档：标题中出现的**12306**单程车票**快速查询模块**测试需求文档，就是测试用例中的模块名称，并且快速查询模块中查询是关键，因此**12306_search**就是测试用例编号抬头，在测试范围内给了四个范围，普通车票就可以没有附加值，学生票、动车高铁、动车高铁学生就可以有附加值：student、gd、student_gd，功能索引填写在测试目的中出现最多的词语比如**车票查询**

   | 测试用例编号 | 模块名称 | 功能索引 | 用例说明         | 前置条件                                                    | 执行步骤                                   | 输入数据   | 预期结果 | 截图文件名       |
   | ------------ | -------- | -------- | ---------------- | ----------------------------------------------------------- | ------------------------------------------ | ---------- | -------- | ---------------- |
   |              |          |          | 是当前用例的概述 | 1.成功进入xx页面；2.xxx处于xx状态，【】或者对**关键字加粗** | 分条说明**完整实现**这条用例要经过哪些步骤 | 加粗、分条 | 对预期的 | 测试用例编号.png |

   ![image-20241017113213623](README.assets/image-20241017113213623.png)
5. 第一项永远填写冒烟用例，也就是什么也不做检查页面完整性
6. 一定要切记，在12306中虽然过早的日期不能直接在弹出框中选中（前端会做校验），**但是可以使用sendKeys输入不合法的值**！！
7. 按照需求说明书来，而不是需求文档
8. 虽然被测软件名\_功能模块名\_附加说明(可选)\_三位数编号，但是附加说明可以是多条，比如：student_gd
9. **先看测试需求文档，再看功能说明书**
10. 出现输入不成功的bug，**试试先点击一下再输入**
11. 要求screenshots**只保留其中一张截图**
12. 在同一个需求下，除了输入数据这种必须变化的以外，预期结果、操作步骤这种等等就写的通用一点，方便速度
13. 虽然但是，已经讲明了是有效等价类，那么前中后都要涉及，这个可以不规则
14. | 10-26 | 11-15 |
    | ----- | ----- |
    |       |       |
    |       |       |
    |       |       |
15. 12306的输入框要**先点击，再输入字符并且回车**，如果是大城市还要退格具体到某个车站
16. 文档里要求了是什么样的地点，那就是这个地点。如果没有硬性规定那么就找最简单好用的
17. 如果预期结果和实际结果相符合，那么直接搬过去
18. 用例数不超过N条，那么最后实际上应该有N-1条
19. 没有提示用例数超过超过多少，那么意味着该项只有一条
20. 文档里面没有直接要求那么就sendKeys，如果指定了控件选择那么就选择控件
21. 前置条件一半是怎么进入这个页面，另一半是谁谁谁是选中状态，谁谁谁是未选中状态
22. 为空并不是为空格，而是没输入
23. 多条用例才使用@CsvSource，否则不用加

    







```
import org.apache.commons.io.FileUtils;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import java.io.File;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Objects;
import java.util.concurrent.TimeUnit;

public class TestCtrip {
    private WebDriver driver;


    @BeforeEach
    public void setup() {
        //提交最终代码脚本时，请将驱动路径换回官方路径"C:\\Users\\86153\\AppData\\Local\\Google\\Chrome\\Application\\chromedriver.exe"
//        System.setProperty("webdriver.chrome.driver", "C:\\Users\\86153\\AppData\\Local\\Google\\Chrome\\Application\\chromedriver.exe");
        ChromeOptions chromeOptions = new ChromeOptions();
        chromeOptions.addArguments("--remote-allow-origins=*");
//        chromeOptions.addArguments("--headless");
//        chromeOptions.addArguments("--disable-gpu");
        driver = new ChromeDriver(chromeOptions);
        driver.get("https://www.ctrip.com");
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

    }

    // test-code-start

    @ParameterizedTest
    @CsvSource({
            "北京,广州,ctrip_R001_001.png",
            "北京,成都,ctrip_R001_002.png",
            "上海,广州,ctrip_R001_003.png",
            "上海,成都,ctrip_R001_004.png"
    })
    public void test_ctrip_R001(String fromStation,String toStation,String filename) throws InterruptedException {
        driver.findElements(By.className("lsn_top_nav_icon_ijnym")).get(3).click();
        Thread.sleep(200);
        WebElement element = driver.findElement(By.id("label-departStation"));
        element.click();
        element.clear();
        element.sendKeys(fromStation);
//        + Keys.ENTER
        Thread.sleep(1000);
        WebElement element1 = driver.findElement(By.id("label-arriveStation"));
        element1.click();
        element1.clear();
//        element1.sendKeys(toStation);
        if (Objects.equals(toStation,"成都")){
            driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[10]")).click();
        }else {
            driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[4]")).click();
        }

        Thread.sleep(200);
//        driver.findElement(By.className("index-top")).click();
        driver.findElement(By.className("ifont-checkbox")).click();

        WebElement element2 = driver.findElement(By.className("ifont-more"));
        element2.click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[2]/ul[2]/li[20]/strong")).click();

        driver.findElement(By.className("btn-search")).click();

        Thread.sleep(3000);
        takeScreenshot(filename);
    }
    // 请在此处插入JUnit5代码
    @ParameterizedTest
    @CsvSource({
            "张三,成都,ctrip_R002_001.png",
            "@#￥,成都,ctrip_R002_002.png"
    })
    public void test_ctrip_R002(String fromStation,String toStation,String filename) throws InterruptedException {
        driver.findElements(By.className("lsn_top_nav_icon_ijnym")).get(3).click();
        Thread.sleep(200);
        WebElement element = driver.findElement(By.id("label-departStation"));
        element.click();
        element.clear();
        element.sendKeys(fromStation);
//        + Keys.ENTER
        Thread.sleep(1000);
        WebElement element1 = driver.findElement(By.id("label-arriveStation"));
        element1.click();
        element1.clear();
//        element1.sendKeys(toStation);
        if (Objects.equals(toStation,"成都")){
            driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[10]")).click();
        }else {
            driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[4]")).click();
        }

        Thread.sleep(200);
//        driver.findElement(By.className("index-top")).click();
        driver.findElement(By.className("ifont-checkbox")).click();

        WebElement element2 = driver.findElement(By.className("ifont-more"));
        element2.click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[2]/ul[2]/li[20]/strong")).click();

        driver.findElement(By.className("btn-search")).click();

        Thread.sleep(3000);
        takeScreenshot(filename);
    }
    // test-code-end
    @Test
    public void test_ctrip_R003() throws InterruptedException {
        driver.findElements(By.className("lsn_top_nav_icon_ijnym")).get(3).click();
        Thread.sleep(200);
        WebElement element = driver.findElement(By.id("label-departStation"));
        element.click();
        element.clear();
        element.sendKeys("北京");
//        + Keys.ENTER
        Thread.sleep(1000);
        WebElement element1 = driver.findElement(By.id("label-arriveStation"));
        element1.click();
        element1.clear();
//        element1.sendKeys(toStation);

        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[2]")).click();


        Thread.sleep(200);
//        driver.findElement(By.className("index-top")).click();
//        driver.findElement(By.className("ifont-checkbox")).click();

        WebElement element2 = driver.findElement(By.className("ifont-more"));
        element2.click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[1]/ul[2]/li[22]/strong")).click();

        driver.findElement(By.className("btn-search")).click();

        Thread.sleep(3000);
        driver.findElements(By.className("ifont-checkbox")).get(0).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[2]/ul/li[1]/strong")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[3]/ul/li[2]")).click();
        Thread.sleep(1000);

        WebElement webElement = driver.findElements(By.className("tit")).get(2);
        webElement.click();
        Thread.sleep(1000);
        webElement.click();
        Thread.sleep(3000);
        takeScreenshot("ctrip_R003_001.png");
    }

    @Test
    public void test_ctrip_R004() throws InterruptedException {
        driver.findElements(By.className("lsn_top_nav_icon_ijnym")).get(3).click();
        Thread.sleep(200);
        WebElement element = driver.findElement(By.id("label-departStation"));
        element.click();
        element.clear();
        element.sendKeys("南京");
//        + Keys.ENTER
        Thread.sleep(1000);
        WebElement element1 = driver.findElement(By.id("label-arriveStation"));
        element1.click();
        element1.clear();
//        element1.sendKeys(toStation);

        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[4]")).click();


        Thread.sleep(200);
//        driver.findElement(By.className("index-top")).click();
//        driver.findElement(By.className("ifont-checkbox")).click();

        WebElement element2 = driver.findElement(By.className("ifont-more"));
        element2.click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[1]/ul[2]/li[22]/strong")).click();

        driver.findElement(By.className("btn-search")).click();

        Thread.sleep(3000);
        driver.findElements(By.className("ifont-checkbox")).get(0).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[2]/ul/li[1]/strong")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[3]/ul/li[1]")).click();
        Thread.sleep(1000);

        driver.findElement(By.className("screen-right-more")).click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[4]/ul/li[1]")).click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[5]/ul/li[1]")).click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[6]/ul/li[1]")).click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[7]/ul/li[1]")).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[7]/div/a")).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[6]/div/a")).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[5]/div/a")).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[4]/div/a")).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[3]/div/a")).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[2]/div/a")).click();
        Thread.sleep(500);//*[@id="__next"]/div/div[3]/div[2]/div[1]/div[2]/div[2]/div/a
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[2]/div[2]/div/a")).click();
        driver.findElements(By.className("ifont-checked")).get(0).click();
        Thread.sleep(500);
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div[2]/div[1]/div[3]")).click();
        Thread.sleep(3000);
        takeScreenshot("ctrip_R004_001.png");
    }

    @ParameterizedTest
    @CsvSource({
            "济南,西安,ctrip_R005_001.png",
            "济南,杭州,ctrip_R005_002.png",
            "天津,西安,ctrip_R005_003.png",
            "天津,杭州,ctrip_R005_004.png"
    })
    public void test_ctrip_R005(String fromStation,String toStation,String filename) throws InterruptedException {
        driver.findElements(By.className("lsn_top_nav_icon_ijnym")).get(3).click();
        Thread.sleep(200);
        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[1]/div/ul/li[2]/i")).click();
        WebElement element = driver.findElement(By.id("label-departStation"));
        element.click();
        element.clear();
        element.sendKeys(fromStation);
//        + Keys.ENTER
        Thread.sleep(1000);
        WebElement element1 = driver.findElement(By.id("label-arriveStation"));
        element1.click();
        element1.clear();
//        element1.sendKeys(toStation);
        if (Objects.equals(toStation,"西安")){
            driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[11]")).click();
        }else {
            driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[2]/div/div[2]/ul[2]/li[3]")).click();
        }

        Thread.sleep(200);
//        driver.findElement(By.className("index-top")).click();
//        driver.findElement(By.className("ifont-checkbox")).click();

        WebElement element2 = driver.findElement(By.className("ifont-more"));
        element2.click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[1]/ul[2]/li[22]")).click();


//        WebElement element3 = driver.findElement(By.id("label-returnDate"));
//        element3.click();
//        Thread.sleep(1000);
//        driver.findElement(By.xpath("//*[@id=\"app\"]/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[2]/ul[2]/li[1]")).click();
        driver.findElement(By.className("btn-search")).click();

        Thread.sleep(3000);
        //展开全部
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div/div[1]/div[2]/div[3]/div")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div/div[1]/div[2]/div[3]/ul/li[1]/div[1]/i")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div/div[1]/div[2]/div[3]/ul/li[2]/div[2]/i")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div/div[1]/div[2]/div[3]/ul/li[3]/div[2]/i")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div/div[1]/div[2]/div[3]/ul/li[4]/div[1]/i")).click();
        driver.findElement(By.xpath("//*[@id=\"__next\"]/div/div[3]/div/div[1]/div[2]/div[3]/ul/li[5]/div[1]/i")).click();
        Thread.sleep(3000);
        takeScreenshot(filename);
    }


    @AfterEach
    public void teardown() {
        this.driver.quit();
    }

    private void takeScreenshot(String fileName) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("HHmmssddSSS");
        String timestamp = dateFormat.format(new Date());
        String timestampedFileName = timestamp + "_" + fileName;
        File screenshotsDir = new File("screenshots");
        if (!screenshotsDir.exists()) {
            screenshotsDir.mkdirs();
        }
        String screenshotFilePath = screenshotsDir.getAbsolutePath() + File.separator + timestampedFileName;
        File screenshotFile = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
        try {
            FileUtils.copyFile(screenshotFile, new File(screenshotFilePath));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```













一些其他的测试用例文档：

1. ![image-20241024154737765](README.assets/image-20241024154737765.png)
2. ![image-20241024154823483](README.assets/image-20241024154823483.png)
3. 开vpn可以访问chatgpt，但是加载国内的网页就特别慢
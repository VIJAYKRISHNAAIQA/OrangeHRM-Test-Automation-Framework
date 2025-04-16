
---

# 🚀 OrangeHRM-Automation: End-to-End Test Automation for OrangeHRM

A **Java + Selenium + TestNG** automation framework designed to test the OrangeHRM demo application. Features include:

- ✅ Maven-based project
- ✅ Page Object Model (POM) design
- ✅ GitHub Actions CI/CD integration
- ✅ Extent Reports for test reporting
- ✅ PR-based QA workflow

---

## 🛠️ Project Setup

### 1. Create GitHub Repository

```bash
git init OrangeHRM-Automation
cd OrangeHRM-Automation
echo "# OrangeHRM Test Automation" >> README.md
git add README.md
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/yourusername/OrangeHRM-Automation.git
git push -u origin main
```

### 2. Initialize Maven Project

```bash
mvn archetype:generate -DgroupId=com.orangehrm -DartifactId=OrangeHRM-Automation -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

### 3. Project Structure

```
OrangeHRM-Automation/
├── src/
│   ├── main/java/com/orangehrm/
│   │   ├── pages/         # Page classes (POM)
│   │   ├── utils/         # Utility classes
│   │   └── App.java       # Main entry (if needed)
│   └── test/java/com/orangehrm/
│       ├── tests/         # Test classes
│       └── listeners/     # TestNG listeners
├── pom.xml               # Maven configuration
├── .github/workflows/    # GitHub Actions workflows
└── test-output/          # Test reports
```

---

## ⚙️ Configure Dependencies (pom.xml)

```xml
<dependencies>
    <!-- Selenium -->
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.14.1</version>
    </dependency>

    <!-- TestNG -->
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.8.0</version>
    </dependency>

    <!-- Extent Reports -->
    <dependency>
        <groupId>com.aventstack</groupId>
        <artifactId>extentreports</artifactId>
        <version>5.1.1</version>
    </dependency>

    <!-- WebDriverManager -->
    <dependency>
        <groupId>io.github.bonigarcia</groupId>
        <artifactId>webdrivermanager</artifactId>
        <version>5.6.3</version>
    </dependency>
</dependencies>
```

---

## 🧱 Implement Page Object Model (POM)

### BasePage.java

```java
package com.orangehrm.pages;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;

public class BasePage {
    protected WebDriver driver;

    public BasePage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }
}
```

### LoginPage.java

```java
package com.orangehrm.pages;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;

public class LoginPage extends BasePage {
    @FindBy(name = "username") private WebElement usernameField;
    @FindBy(name = "password") private WebElement passwordField;
    @FindBy(css = "button[type='submit']") private WebElement loginButton;

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    public void login(String username, String password) {
        usernameField.sendKeys(username);
        passwordField.sendKeys(password);
        loginButton.click();
    }
}
```

---

## 🧪 Write Test Cases (TestNG)

### BaseTest.java

```java
package com.orangehrm.tests;

import com.orangehrm.pages.LoginPage;
import io.github.bonigarcia.wdm.WebDriverManager;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;

public class BaseTest {
    protected WebDriver driver;
    protected LoginPage loginPage;

    @BeforeMethod
    public void setUp() {
        WebDriverManager.chromedriver().setup();
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://opensource-demo.orangehrmlive.com/web/index.php/auth/login");
        loginPage = new LoginPage(driver);
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### LoginTest.java

```java
package com.orangehrm.tests;

import org.testng.Assert;
import org.testng.annotations.Test;

public class LoginTest extends BaseTest {
    @Test
    public void testSuccessfulLogin() {
        loginPage.login("Admin", "admin123");
        Assert.assertTrue(driver.getCurrentUrl().contains("/dashboard"));
    }

    @Test
    public void testInvalidLogin() {
        loginPage.login("invalidUser", "invalidPass");
        Assert.assertTrue(driver.getPageSource().contains("Invalid credentials"));
    }
}
```

---

## 🔄 GitHub Actions CI Setup

### `.github/workflows/maven.yml`

```yaml
name: Java CI with Maven

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'
      - name: Build with Maven
        run: mvn clean test
      - uses: actions/upload-artifact@v3
        if: always()
        with:
          name: test-reports
          path: test-output/
```

---

## 📊 Execute Tests & Generate Reports

### Run Tests Locally

```bash
mvn clean test
```

### View Extent Reports

After execution, check:

```
test-output/ExtentReport.html
```

---

Certainly! Continuing from where we left off, here's the detailed Git workflow for QA engineers, focusing on the OrangeHRM demo application:

---

## 🌿 Git Workflow for QA Engineers

### Branching Strategy
Create a dedicated branch for your test cases to ensure isolation and clarity

```bash
git checkout -b test/login-validation
git add src/test/java/com/orangehrm/tests/LoginTest.java
git commit -m "TEST: Add login validation tests"
git push origin test/login-validation
```

### Create Pull Request (PR)

1.Navigate to GitHub → **Pull Requests** → **New Pull Request*
2.Select `test/login-validation` as the compare branch and `main` as the base branc
3.Add reviewers (e.g., Developer, Senior QA
4.PR Title: `QA: Login Validation Tests for OrangeHRM
5.Provide a detailed description
   -**Purpose**: Validate login functionality with various credential scenario
   -**Test Cases**
     - Valid username and password
     - Invalid username
     - Invalid password
     - Empty fields
   -**Test Data**
     - Valid Credentials: `Admin` / `admin123`
     - Invalid Credentials: `InvalidUser` / `InvalidPass`
   -**Test Results**
     - All tests passed successfully on local environment
     - Screenshots attached for failed scenarios

6.Link related issues or user stories, if applicabl
7.Submit the pull request for revie

---
By following this workflow, QA engineers can ensure that their test cases are properly integrated into the project, reviewed by peers, and maintained in a structured manner

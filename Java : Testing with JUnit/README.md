# Java : Testing with JUnit

Unit testing is the process of examining a small piece of software. Integration tests are for a whole component, they test the entire system. You can download Junit 4 from the GitHub. You will need in particular the following files `hamcrest-core-version.jar` and the `junit-version.jar`.

Assertions can be used to compare expected and actual values. An example is :

```
@Test
public void testAssertArrayEquals() {
  byte[] expected = "test".getBytes();
  byte[] actual = "test".getBytes();
  assertArrayEquals("failure - bytes array are not the same", expected, actual);
}

@Test
public void testAssertEquals() {
  assertEquals("failure - strings are not equal", "text", "text");
}

@Test
public void testAssertFalse() {
  assertFalse("failure - should be false", false);
}

@Test
public void testAssertNotNull () {
  assertNotNull("should not be null", new Object()); 
}

@Test 
public void testAssetNotSame() {
  assertNotSame("should not be same objects", new Object(), new Object());
}

@Test
public void testAssertNull () {
  assertNull("should be null", null);
}

@Test
public void testAssertSame() {
  Integer aNumber = Integer.valueOf(62);
  assetSame("should be same", aNumber, aNumber);
}

@Test
public void testAssertTrue() {
  assertTrue("failure - should be true", true);
}
```

Running a test suite, runs all the tests in that suite in a specific order. An example is :

```
package testsuite;
import org.junit.Assert;
import org.junit.Test;

public class SuiteTest1 {
  @Test
  public void testPrintMessage() {
    int num = 5;
    System.out.println("Suite Test1 is executing");
    Assert.assertEquals(5, num);
  }
}
```
The second test suite is :

```
package testsuite;
import org.junit.Assert;
import org.junit.Test;

public class SuiteTest2 {
  @Test
  public void testPrintMessage() {
    String hello = "Hello World";
    System.out.println("Suite Test2 is executing");
    Assert.assertNotNull(hello);
    Assert.assertEquals("Hello World", hello);
  }
}
```
The suite main method is :

```
import org.junit.Assert.*;

@RunWith(Suite.class)
@Suite.SuiteClasses({
  SuiteTest1.class,
  SuiteTest2.class,
})

public class TestSuiteTest {

  public TestSuiteTest() {}
  
  @BeforeClass
  public static void stUpClass() throws Exception {}
  
  @AfterClass
  public static void tearDownClass() throws Exception {}
}
```

Here we want to verify that instead the mini programs throw the exepcted exception :

```
package exceptionhandling;
import java.util.ArrayList;
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;

public class ExceptionHandlingTest {

  public ExceptionHandlingTest () {}
  
  @BeforeClass
  public static void setUpClass() {}
  
  @AfterClass
  public static void tearDownClass () {}
  
  @Test (expected = IndexOutOfBoundException.class)
  public void testMain() {
    new ArrayList<Object>().get(0);
  }
  
  @Test(expected = ArithmeticException.class)
  public void testDivideByZero() {
    int  = 10/0;
  }
}
```
You can also ignore certain tests as follows :

```
@Ignore("Test later")
@Test
public void testMain() {
  assertFalse(true);
}
```
Let's try tu run parametrized test where we check that the sum of the first two entries in the first two columns eaulas the entry in the last column.

```
package parametrizedexample;
import java.util.Arrays;
import java.util.Collections;
import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parametrized;
import org.junit.runners.Parametrized.Parameters;

@RunWith(value = Parametrized.class)
public class parametrizedTest {
  private int numA;
  private int numB;
  private int expected;
  
  public parametrizedTest(int numA, int numB, int expected) {
    this.numA = numA;
    this.numB = numB;
    this.expected = expected;
  }
  
  @Parameters
  public static Collection<Object[]> data() {
    return Arrays.asList(new Object[][] {
      {1, 1, 2}, 
      {2, 2, 4},
      {3, 2, 5},
      {4, 5, 9},
      {5, 5, 10},
      {6, 6, 12},
      {7, 8, 15},
    });
  }
  
  @Test
  public void test_addTwoNumbers() {
    assertThat(MathUtils.add(numA, numB), is(expected));
  }
}
```

## Eclipse-Robolectric-Sample, a pre-configured Robolectric example
  
  
  
  

My blog: [Public Static Droid Main](http://publicstaticdroidmain.com/)  
Robolectric website: [Robolectric](http://pivotal.github.com/robolectric/)  
Michael Portuesi: [Digital Dumptruck](http://digitaldumptruck.jotabout.com/)  


This is a pre-configured boilerplate project to help people configure Robolectric in Eclipse and Ant without the use of Maven. [Michael Portuesi](http://digitaldumptruck.jotabout.com/) recently helped me sort this out and I decided to share it to help other people get started. 

## How do I use it?
After cloning this repository, add the android-project and android-project-test projects into an Eclipse workspace. The android-project is an Android project (obviously), and the android-project-test project is a plain old Java project. You should be able to right-click on the android-project-test project, and choose "Run As -> JUnit Test", and the single test should run via Robolectric and JUnit. Alternatively, you should be able to right-click on the build.xml file and choose "Run As -> Ant Build". This ant script can be used to run your Robolectric tests via a CI server like Jenkins. 

Take note of the SampleTestRunner class in com.justinschultz.test.runner, as this is the "secret sauce" that allows Robolectric to find your Android project's manifest and resources. This class inherits from RobolectricTestRunner and should be used to run all of your tests. 

## Writing Tests
When authoring tests, make sure your class names end with the word "Test", as the ant script requires this. Also make sure that its @RunWith annotation uses the SampleTestRunner.class, and NOT the RobolectricTestRunner.class:
<pre>
@RunWith(SampleTestRunner.class)
public class SampleTest {
    @Test
    public void testBasicResourceValue() throws Exception {
        String helloFromActivity = new MainActivity().getResources().getString(R.string.hello);
        assertThat(helloFromActivity, equalTo("Hello World, MainActivity!"));
    }
}
</pre>

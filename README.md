## Eclipse-Robolectric-Sample, a pre-configured Robolectric example  
&nbsp;  
Blog: [Public Static Droid Main](http://publicstaticdroidmain.com/2012/03/robolectric-ant-project-eclipse/)
## What is this?
This is a pre-configured boilerplate project to help people configure Robolectric in Eclipse and Ant without the use of Maven. [Michael Portuesi](http://digitaldumptruck.jotabout.com/) recently helped me sort this out and I decided to share it to help other people get started. 

## Setup
After cloning this repository, add the android-project and android-project-test projects into an Eclipse workspace. The android-project is an Android project (obviously), and the android-project-test project is a plain old Java project. You should be able to right-click on the android-project-test project, and choose "Run As -> JUnit Test", and the single test should run via Robolectric and JUnit. Alternatively, you should be able to right-click on the build.xml file and choose "Run As -> Ant Build". This ant script can be used to run your Robolectric tests via a CI server like Jenkins. 

Take note of the SampleTestRunner class in com.justinschultz.test.runner, as this is the "secret sauce" (thanks Michael!) that allows Robolectric to find your Android project's manifest and resources. This class inherits from RobolectricTestRunner and should be used to run all of your tests:
<pre>
package com.justinschultz.tests.runner;

import java.io.File;

import org.junit.runners.model.InitializationError;

import com.xtremelabs.robolectric.RobolectricConfig;
import com.xtremelabs.robolectric.RobolectricTestRunner;

public class SampleTestRunner extends RobolectricTestRunner {

	/**
	 * Call this constructor to specify the location of resources and AndroidManifest.xml.
	 * 
	 * @param testClass
	 * @throws InitializationError
	 */	
	public SampleTestRunner(@SuppressWarnings("rawtypes") Class testClass) throws InitializationError {
		super(testClass, new RobolectricConfig(new File("../android-project/AndroidManifest.xml"), new File("../android-project/res")));
	}
}
</pre>

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
  
Finally, make sure junit.jar is defined in your Ant script’s classpath BEFORE android.jar, otherwise Ant will throw an error:  
<pre>
&lt;path id=&quot;junit_classpath&quot;&gt;
    &lt;pathelement path=&quot;${build.dir}&quot;/&gt;
    &lt;pathelement path=&quot;${android.project.classpath}&quot;/&gt;
    &lt;!-- NOTE: junit.jar must come before android.jar! --&gt;
    &lt;filelist refid=&quot;libs_jars&quot;/&gt;
    &lt;filelist refid=&quot;android_jars&quot;/&gt;
&lt;/path&gt;
</pre>

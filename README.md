# puppeteer-jasmine-reporter
A Puppeteer reporter plugin for JasmineBDD, that outputs functional test results in JUnit xml format for inclusion in Jenkins 

# about
This is a small reporter that was built to allow reporting fron JasmineBDD to be picked up by Jenkins. Jenkins supports JUnit 
tests and can run headless testing, in this case provided by Puppeteer. 

# usage 

It is assumed that JasmineBDD, Puppeteer and Jenkins are familiar technologies. 

* create a Jasmine test html page. 
* npm install this reporter 
* include the reporter in your html file. 

```
<link rel="stylesheet" type="text/css" href="jasmine-2.6.4/jasmine.css">
<script type="text/javascript" src="jasmine-2.6.4/jasmine.js"></script>
<script type="text/javascript" src="jasmine-2.6.4/jasmine-html.js"></script>
<script type="text/javascript" src="puppeteer-jasmine-reporter/junit_reporter.js"></script>
<script type="text/javascript" src="jasmine-2.6.4/boot.js"></script>
<script>
    jasmine.getEnv().addReporter(
        new jasmineReporters.JUnitXmlReporter({
            savePath: '..',
            consolidateAll: false
        })
    );
</script>
```

The test should now run as normal, the default html report displaying the results to screen and the junit-reporter writing 
XML to the console. The console output would be similar to this...

```
<?xml version="1.0" encoding="UTF-8" ?>
<testsuites>
    <testsuite name="my.test.suite" timestamp="2018-01-11T11:15:55" hostname="localhost" time="0.018" errors="0" tests="14" skipped="0" disabled="0" failures="0">
        <testcase classname="my.test.suite" name="Do test one" time="0.005" />
        <testcase classname="my.test.suite" name="Do test two" time="0" />
    </testsuite>
</testsuites>
```

* Set up a Puppeteer script to run the test ( I use nano-server host the tests remotely )
* Add a file writer into the script 

```
// Listen for console.log requests
page.on('console', ( msg ) => {
    // Derive filename from Url currently being browsed to
    let filename = page.url().substring( page.url().lastIndexOf( '/' ) + 1 );
    filename = filename.replace( /html/g, 'xml' );

    fs.writeFileSync( filename, msg.text );
} );
```

This is not a full solution as Jasmine tests can be nested, but should suffice for simple tests. 
This uses the current page url to generate a similar filename to store the xml under, which can 
then be read directly by Jenkins JUnit for generating a nice graph. 


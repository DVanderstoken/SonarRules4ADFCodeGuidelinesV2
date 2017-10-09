# Sonar Rules for ADF Code Guidelines V2

## ADFcg1-01012

There are a number of web.xml settings that can be set to assist the process of debugging during development and testing:

```
oracle.adf.view.rich.ASSERT_ENABLED=true
org.apache.myfaces.trinidad.DEBUG_JAVASCRIPT=true
org.apache.myfaces.trinidad.DISABLE_CONTENT_COMPRESSION=true
oracle.adf.view.rich.LOGGER_LEVEL=true
```

Be aware that you might not want to have these set for all testing systems such as user acceptance testing and performance and stress testing, where the perception of speed to your users is paramount.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>/web-app/context-param[count(param-name/text/@Image = 'oracle.adf.view.rich.ASSERT_ENABLED') > 0 and param-value/text/@Image != 'true' and (count(param-name/text/@Image = 'org.apache.myfaces.trinidad.DEBUG_JAVASCRIPT') > 0 and param-value/text/@Image != 'true') and (count(param-name/text/@Image = 'org.apache.myfaces.trinidad.DISABLE_CONTENT_COMPRESSION') > 0 and param-value/text/@Image != 'true') and (count(param-name/text/@Image = 'oracle.adf.view.rich.LOGGER_LEVEL') > 0 and param-value/text/@Image != 'true')]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Consider web.xml settings for development and testing</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01013

Ensure that the following context parameters are set for a production environment:

```
- oracle.adf.view.rich.automation.ENABLE=false [default value]
- oracle.adf.view.rich.ASSERT_ENABLED=false [default value]
- org.apache.myfaces.trinidad.CHECK_FILE_MODIFICATION=false [default value]
- org.apache.myfaces.trinidad.COMPRESS_VIEW_STATES=true
- org.apache.myfaces.trinidad.DEBUG_JAVASCRIPT=false
- org.apache.myfaces.trinidad.DISABLE_CONTENT_COMPRESSION=false [default value]
- oracle.adf.view.rich.libraryPartitioning.DISABLED=false [default value]
- oracle.adf.view.rich.LOGGER_LEVEL=false [default value]
- javax.faces.STATE_SAVING_METHOD=client
```

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>/web-app/context-param[count(param-name/text/@Image = 'oracle.adf.view.rich.automation.ENABLE') &gt; 0 and param-value/text/@Image != 'false' and (count(param-name/text/@Image = 'oracle.adf.view.rich.ASSERT_ENABLED') &gt; 0 and param-value/text/@Image != 'false') and (count(param-name/text/@Image = 'org.apache.myfaces.trinidad.CHECK_FILE_MODIFICATION') &gt; 0 and param-value/text/@Image != 'false') and (count(param-name/text/@Image = 'org.apache.myfaces.trinidad.COMPRESS_VIEW_STATES') &gt; 0 and param-value/text/@Image != 'true') and (count(param-name/text/@Image = 'org.apache.myfaces.trinidad.DEBUG_JAVASCRIPT') &gt; 0 and param-value/text/@Image != 'false') and (count(param-name/text/@Image = 'org.apache.myfaces.trinidad.DISABLE_CONTENT_COMPRESSION') &gt; 0 and param-value/text/@Image != 'false') and (count(param-name/text/@Image = 'oracle.adf.view.rich.libraryPartitioning.DISABLED') &gt; 0 and param-value/text/@Image != 'false') and (count(param-name/text/@Image = 'oracle.adf.view.rich.LOGGER_LEVEL') &gt; 0 and param-value/text/@Image != 'false') and (count(param-name/text/@Image = 'javax.faces.STATE_SAVING_METHOD') &gt; 0 and param-value/text/@Image != 'client') ]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Ensure web.xml settings for production</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01014

Set the web.xml `oracle.adf.view.rich.versionString.HIDDEN` context parameter to true to remove the ADF Faces version information on each page to reduce the size of the page and also remove an attack vector by unnecessarily publishing the version of ADF.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>/web-app/context-param[param-name/text/@Image = 'oracle.adf.view.rich.versionString.HIDDEN' and param-value/text/@Image != 'true']</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Consider oracle.adf.view.rich.versionString.HIDDEN=false for production</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01015

Use the `oracle.adf.view.rich.security.FRAME_BUSTING` context parameter to use framebusting in your application. 

Framebusting is a way to prevent clickjacking, which occurs when a malicious web site pulls a page from another domain into a frame and overlays it with a counterfeit page, allowing only portions of the original, or clickjacked, page (for example, a button) to display. When the user clicks the button, they in fact are clicking a button on the clickjacked page, causing unexpected results.

For example, say your application is a web-based email application that resides in DomainA and a web site in DomainB clickjacks your page by creating a page with an IFrame that points to a page in your email application at DomainA. When the two pages are combined, the page from DomainB covers most of your page in the IFrame, and exposes only a button on your page that deletes all email for the account. The user, not knowing they are actually in the email application may click the button and inadvertently delete all their email.

Framebusting prevents clickjacking by using the following JavaScript to block the application's pages from running in frames:
`top.location.href = location.href;`

If you configure your application to use framebusting by setting the parameter to always, then whenever a page tries to run in a frame, an alert is shown to the user that the page is being redirected, and then the above code is run, which causes it to be the topmost page, thereby disallowing the page to run in the frame.

If your application needs to use frames, you can set the parameter value to `differentDomain`. This setting causes framebusting to occur only if the frame is in a page that is from a different domain than your application. This is the default setting.
For example, say you have a page named DomainApage1 in your application that uses a frame to include the page DomainApage2. Say the external DomainBpage1 tries to clickjack the page DomainApage1. The result would be the following window hierarchy:
-	DomainBpage1
-	DomainApage1
-	DomainApage2

If the application has framebusting set to be differentDomain, then the framework walks the parent window hierarchy to determine whether any ancestor windows are from a different domain. Because DoaminBpage1 is from a different domain, the framebusting JavaScript code will run for the DomainApage1 page, causing it to become the top-level window. And because DomainApage2 is from the same domain as DomainApage1, it will be allowed to run in the frame.

Valid values are:
- `always`: The page will show an error and redirect whenever it attempts to run in a frame.
- `differentDomain`: The page will show an error and redirect only when it attempts to run in a frame on a page in a different domain (the default).
- `never`: The page can run in any frame on any domain.

This context parameter is ignored and will behave as if it were set to never when either of the following context parameters is set to true:
- `org.apache.myfaces.trinidad.util. ExternalContextUtils.isPortlet`
- `oracle.adf.view.rich.automation.ENABLED`


XPath Rule:
```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>INFO</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>/web-app/context-param[count(param-name/text/@Image = 'oracle.adf.view.rich.security.FRAME_BUSTING') &amp;amp;amp;amp;gt; 0 and param-value/text/@Image = 'never']</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Consider setting oracle.adf.view.rich.security.FRAME_BUSTING for production</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01017

Do not use any of the following ADF packages within your Java code as Oracle does not guarantee to change the implementation in the future:

```
- oracle.adfinternal
- oracle.adf.controller.internal
- org.apache.myfaces.trinidadinternal
- oracle.webcenter.internal
- oracle.webcenter.*.internal
```

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>//ImportDeclaration/Name[@Image='oracle.adfinternal' or @Image='oracle.webcenter.internal' or @Image='oracle.adf.controller.internal' or @Image='org.apache.myfaces.trinidadinternal']</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Don't use internal ADF packages</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01018

Executing System.exit() in a running ADF application will force the JVM container to quit. Do not use this.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters> 
    <parameter>
      <key>xpath</key> 
      <value>//Statement/StatementExpression/PrimaryExpression/PrimaryPrefix/Name[@Image='System.exit']</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Don't use System.exit()</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01023-log4j

The Log4j logger allows you to write conditions that test the current log level and therefore only embark on expensive log message creation.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>//PrimaryPrefix[ (ends-with(Name/@Image, '.debug') and count(../descendant::AdditiveExpression) &gt; 0 and count(ancestor::IfStatement/Expression/descendant::PrimaryExpression[ ends-with(descendant::PrimaryPrefix/Name/@Image, 'isDebugEnabled') or ends-with(descendant::PrimaryPrefix/Name/@Image, 'isEnabledFor' and ends-with(descendant::PrimaryPrefix/Name/@Image, 'DEBUG'))] ) = 0) or (ends-with(Name/@Image, '.trace') and count(../descendant::AdditiveExpression) &gt; 0 and count(ancestor::IfStatement/Expression/descendant::PrimaryExpression[ ends-with(descendant::PrimaryPrefix/Name/@Image, 'isTraceEnabled') or ends-with(descendant::PrimaryPrefix/Name/@Image, 'isEnabledFor' and ends-with(descendant::PrimaryPrefix/Name/@Image, 'TRACE'))] ) = 0) or (ends-with(Name/@Image, '.info') and count(../descendant::AdditiveExpression) &gt; 0 and count(ancestor::IfStatement/Expression/descendant::PrimaryExpression[ ends-with(descendant::PrimaryPrefix/Name/@Image, 'isInfoEnabled') or (ends-with(descendant::PrimaryPrefix/Name/@Image, 'isEnabledFor') and ends-with(descendant::PrimaryPrefix/Name/@Image, 'INFO') )] ) = 0) or (ends-with(Name/@Image, '.error') and count(../descendant::AdditiveExpression) &gt; 0 and count(ancestor::IfStatement/Expression/descendant::PrimaryExpression[ ends-with(descendant::PrimaryPrefix/Name/@Image, 'isEnabledFor') and ends-with(descendant::PrimaryPrefix/Name/@Image, 'ERROR')] ) = 0) or (ends-with(Name/@Image, '.fatal') and count(../descendant::AdditiveExpression) &gt; 0 and count(ancestor::IfStatement/Expression/descendant::PrimaryExpression[ ends-with(descendant::PrimaryPrefix/Name/@Image, 'isEnabledFor') and ends-with(descendant::PrimaryPrefix/Name/@Image, 'FATAL')] ) = 0) or (ends-with(Name/@Image, '.warn') and count(../descendant::AdditiveExpression) &gt; 0 and count(ancestor::IfStatement/Expression/descendant::PrimaryExpression[ ends-with(descendant::PrimaryPrefix/Name/@Image, 'isEnabledFor') and ends-with(descendant::PrimaryPrefix/Name/@Image, 'WARN')] ) = 0) ]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Guard log conditions with Log4j</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-01028

Do not use functions like System.out.println.for debugging and do not leave this unnecessarily in your code.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>//PrimaryPrefix/Name[@Image='System.out.println' or @Image='System.err.println']</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Don't use System.out.* or System.err.*</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-02005

The model project should not import or use classes from the view or controller layers such as `oracle.adf.controller`, `oracle.adf.view` or `org.apache.myfaces.trinidad` otherwise they are in violation of the MVC pattern.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>INFO</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>ImportDeclaration[ (contains(lower-case(Name//@Image), '.view.') or contains(lower-case(Name//@Image), '.faces.') or contains(lower-case(Name//@Image), '.controller.') or contains(lower-case(Name//@Image), '.ui')) and /PackageDeclaration[contains(lower-case(Name//@Image), 'model')] ]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Do not import view or controller classes</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-02024

Entity objects should not access application modules as this makes the entity object only useable within that application module.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>INFO</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>ImportDeclaration[ contains(lower-case(Name//@Image), 'model.services.') and /PackageDeclaration[contains(lower-case(Name//@Image), 'model.bussinessobjects')] ]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Do not access an application module from an entity object</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-02034

Do not override an entity object's EntityImpl `validateEntity()` method. Rather code any business logic as a declarative method validator for the entity object itself. This comes with the added advantage all validations are visible in the entity object's business rules page and have all the features available to the declarative validators.

XPath rule:

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>INFO</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>//MethodDeclaration[ contains(MethodDeclarator/@Image, 'validateEntity') and /PackageDeclaration[contains(lower-case(Name//@Image), 'model.bussinessobjects')] ]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Avoid using the entity object validateEntity() method for validation</value> 
    </parameter>
  </parameters>
</rule>
```


## ADFcg1-03088

Do not use the JSF navigationHandler directly. This bypasses the JSF lifecycle and important parts of ADFs processing (e.g. Metadata commit).

XPath rule: 

```
<rule>
  <repositoryKey>pmd</repositoryKey> 
  <key>XPathRule_xxxxxxxxxx</key> 
  <priority>BLOCKER</priority> 
  <parameters>
    <parameter>
      <key>xpath</key> 
      <value>//ImportDeclaration[contains(lower-case(Name/@Image),'navigationhandler')]</value> 
    </parameter>
    <parameter>
      <key>message</key> 
      <value>Perform programmatic navigation with queueActionEventInRegion,setViewId on the controllerContext or queued ActionEvent on a hidden component</value> 
    </parameter>
  </parameters>
</rule>
```


# Welcome to the Shared Code project
The [SmarterApp](http://smarterapp.org) Shared Code contains code to be used across all SmarterApp projects.

## License ##
This project is licensed under the [AIR Open Source License v1.0](http://www.smarterapp.org/documents/American_Institutes_for_Research_Open_Source_Software_License.pdf).

## Getting Involved ##
We would be happy to receive feedback on its capabilities, problems, or future enhancements:

* For general questions or discussions, please use the [Forum](forum_link_here).
* Use the **Issues** link to file bugs or enhancement requests.
* Feel free to **Fork** this project and develop your changes!

## Shared Logback Configuration ##
To use the shared logback configuration, a project with sb11-shared-code.jar dependency just needs a logback.xml file that looks like

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration scan="true">
		<property scope="local" name="app.context.name" value="my-app.rest" />
	    <property scope="local" name="app.base.package.name" value="org.opentestsystem.authoring.basepkgname" />
	    <property scope="local" name="app.base.package.loglevel" value="debug" />
		<include resource="logback-included-common-config.xml" />
	</configuration>

* "app.context.name" -- this value will be the context name used for both the folder name in which logfiles are created and the filename prefix
* "app.base.package.name" -- property definition for package name prefix to observe, assumed that application's packages share same base name
* "app.base.package.loglevel" -- desired logging level for observed package name prefix
* "logback-included-common-config.xml" -- name of the shared logback configuration residing in the sb11-shared-code.jar


Shared Web Build Info Configuration
=================================================
The sb11-shared project provides a mechanism for viewing your project's current build information, including:

1. Application Version
1. Jenkins Build Number
1. Jenkins Build Date
1. Jenkins Build Url
1. Git Revision (SHA)
1. Manifest Version
        
To use the shared web build info configuration, a project with sb11-shared-code.jar needs to:

1. Include the appropriate maven dependencies and plugins (see below)
1. Include the shared-web-context.xml spring context file (see below)

### Maven Dependencies
Include the following dependency to your project with the appropriate version number:

	<dependency>
		<groupId>org.opentestsystem.shared</groupId>
		<artifactId>sb11-shared-code</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

Also include the following maven plugin to your project with the appropriate version number.  This will instruct Maven to add build information from Jenkins to your project's MANIFEST.MF file.
        
	<plugin>
		<artifactId>maven-war-plugin</artifactId>
		<version>2.4</version>
		<configuration>
			<archive>
				<manifest>
					<addDefaultImplementationEntries>true</addDefaultImplementationEntries>
				</manifest>
				<manifestEntries>
					<Specification-Version>${project.version}</Specification-Version>
					<Implementation-Version>${BUILD_NUMBER}</Implementation-Version>
					<Implementation-Date>${BUILD_ID}</Implementation-Date>
					<Implementation-Identifier>${GIT_COMMIT}</Implementation-Identifier>
					<Implementation-Url>${BUILD_URL}</Implementation-Url>
				</manifestEntries>
			</archive>
		</configuration>
	</plugin>
        
### Configuration to serve the Version Controller
The sb11-shared-code project includes a Spring MVC annotated controller that will provide a built-in /version endpoint for you.  
To utilize the controller, you must add an import to your spring context file which will do an annotation scan at startup.  This process will wire up a Spring MVC controller that will parse your project's MANIFEST.MF file and display the build info on a /version endpoint.

The sb11-shared-code context import looks like this:

       <import resource="classpath:shared-web-context.xml" />

Build Info Configuration for projects using JSF
=================================================

* Follow the steps mentioned above under "Shared Web Build Info Configuration".
* Add managed bean ```buildInfo``` into the ```faces-config.xml``` file for your project. For example:
```
<managed-bean>
	<managed-bean-name>buildInfoBacking</managed-bean-name>
	<managed-bean-class>TDS.Shared.Web.backing.BuildInfoBacking</managed-bean-class>
	<managed-bean-scope>session</managed-bean-scope>
</managed-bean>
```
* Add the following inside your CSS if it doesn't already exist. Make sure .footer already exists.
```
.footer .footerText {
    color: white;
    padding-top: 4px;
}
```
*  Inside your xhtml file where you want to show build info in footer, add the following code in place of the footer.
```
<div class="footer">
	<h:panelGroup layout="block" class="footerText" rendered="#{buildInfoBacking.appVersionFound}">
	     Version: #{buildInfoBacking.buildInfo.appVersion}
	     <h:panelGroup rendered="#{buildInfoBacking.jenkinsBuildNumberFound}"> (build #: #{buildInfoBacking.buildInfo.jenkinsBuildNumber})</h:panelGroup>
	</h:panelGroup>
</div>
```
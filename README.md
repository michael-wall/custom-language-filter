## Introduction ##
- This is a custom Language Filter based on the out of the box com.liferay.portal.servlet.filters.language.LanguageFilter.
- It is intended to control when the Language Filter sets Cache-Control header to 'private, no-cache' rather than always setting it for all requests processed by the Language Filter.
  - In a 'vanilla' local Liferay DXP environment (i.e. without a CDN or reverse proxy etc.) the requests that match will have a Cache-Control header of "max-age=315360000, public".
  - It does NOT change when the core Language Filter logic (i.e. the translation logic) runs.
  - It does NOT change the setting of Cache-Control header or other cache related headers elsewhere in the Liferay DXP codebase. However the absense of Cache-Control header after processing by the Language Filter may change the behaviour elsewhere in the Liferay DXP codebase. 
- Custom logic:
  - If the cacheControlBypassUri property is not null and the request URI starts with the specified value, the custom LanguageFilter does not set the Cache-Control header.
  - If the cacheControlBypassCombo property is true, the request URI starts with /combo and languageId is populated, the custom LanguageFilter does not set the Cache-Control header.

## Setup Steps ##
- Build the JAR.
- Copy the JAR to \tomcat\webapps\ROOT\WEB-INF\shielded-container-lib
- Update \tomcat\webapps\ROOT\WEB-INF\liferay-web.xml as follows:
  - Replace
  ```
  <filter>
  	<filter-name>Language Filter</filter-name>
  	<filter-class>com.liferay.portal.servlet.filters.language.LanguageFilter</filter-class>
  </filter>
  ```
  - With
  ```
  <filter>
  	<filter-name>Language Filter</filter-name>
  	<filter-class>com.mw.custom.servlet.filters.LanguageFilter</filter-class>
  </filter>
  ```
- Add and configure the custom portal properties
  ```
  com.mw.custom.servlet.filters.LanguageFilter.cacheControlBypassCombo=true
  com.mw.custom.servlet.filters.LanguageFilter.cacheControlBypassUri=xxx
  ```
  - For example use /o/xxxxx-theme-xxx/js/ to bypass setting the Cache-Control header on any file whose uri starts with /o/xxxxx-theme-xxx/js/
  - Don't include the context if the environment uses a non-standard context
- Complete the steps on each node in the cluster.
 
## TODO ##
- Update com.mw.custom.servlet.filters.LanguageFilter.cacheControlBypassUri to be a comma separated array (for example) and update the custom logic in processFilter to match.
 
## Notes ##
- This is a ‘proof of concept’ that is being provided ‘as is’ without any support coverage or warranty.
- The creating and sharing of this ‘proof of concept’ should not be considered a recommendation to use it or an endorsement of the approach used by the ‘proof of concept’.
- Ensure it is fully tested in a non-production environment (ideally under load) before considering using in a Production environment.
- The implementation uses a custom JAR meaning it is compatible with Liferay DXP Self-Hosted and Liferay PaaS, but is not compatible with Liferay SaaS.
- The implementation was tested locally using Liferay DXP 2025.Q1.0 LTS.
- JDK 21 is expected for both compile time and runtime.

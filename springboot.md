SpringApplication.run()

​    1.new SpringApplication()

​          webApplicationType  --SERVLET

​          setInitializers

​          setListeners

​    2.prepareEnvironment()

​           getOrCreateEnvironment()

​		StandardServletEnvironment()

​			customizePropertySources

​				servletConfigInitParams

​                                servletContextInitParams

​				environmentBlock

​				getSystemProperties

​				getSystemEnvironment

3.createApplicationContext()

4.prepareContext()

5.refreshContext()

6.afterRefresh()
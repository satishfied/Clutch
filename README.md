# Clutch

## Clutch.Diagnostics.EntityFramework

Provides api for tracing EntityFramework sql commands.

Example configuration for web project:

```c#
protected void Application_Start()
{
    DbTracing.Enable(
        new GenericDbTracingListener()
            .OnFinished(c => logger.Trace("-- Command finished - time: {0}{1}{2}", c.Duration, Environment.NewLine, c.Command.ToTraceString()))
            .OnFailed(c => logger.Trace("-- Command failed - time: {0}{1}{2}", c.Duration, Environment.NewLine, c.Command.ToTraceString()))
    );
}
```

## Clutch.Diagnostics.Logging.NLog

Extends log messages of NLog with Clutch.Diagnostics.Logging and writes to log file using xml.

Example NLog configuration:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      autoReload="true">

  <extensions>
    <add assembly="Clutch.Diagnostics.Logging.NLog" />
  </extensions>

  <targets>
    <target xsi:type="File" name="file" encoding="UTF-8" fileName="${basedir}/App_Data/logs/log.txt" archiveNumbering="Rolling"
            maxArchiveFiles="28" archiveEvery="Day" archiveFileName="${basedir}/App_Data/logs/log_{##}.txt"
            createDirs="true" autoFlush="true">
      <layout xsi:type="ClutchXml" />
    </target>

    <target name="debugger" xsi:type="Debugger"
            layout="${longdate}|${level:uppercase=true}|${logger}|${message}|${exception:format=tostring}" />
  </targets>

  <rules>
    <logger name="*" minlevel="Debug" writeTo="file" />
    <logger name="*" minlevel="Trace" writeTo="debugger" />
  </rules>
</nlog>
```
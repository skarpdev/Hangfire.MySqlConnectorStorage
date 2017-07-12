# Hangfire MySql Storage Implementation
[![Latest version](https://img.shields.io/nuget/v/Hangfire.MySqlConnectorStorage.svg)](https://www.nuget.org/packages/Hangfire.MySqlConnectorStorage/) 

MySql storage implementation of [Hangfire](http://hangfire.io/) - fire-and-forget, delayed and recurring tasks runner for .NET. Scalable and reliable background job runner. Supports multiple servers, CPU and I/O intensive, long-running and short-running jobs.

This repo is a fork of: [Hangfire.MySqlStorage](https://github.com/arnoldasgudas/Hangfire.MySqlStorage) so all credits go to `arnoldasgudas` for all the initial footwork.
This connector is different from the original by:

- using the true async `MySqlConnector` package 

*Why?*

If you are using [Pomelo EntityFrameworkCore Mysql](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) you will get the `MySqlConnector` assembly, which is a drop in replacement for `MySQL.Data` - having both assemblies in a project kills it, as there are now conflicting namespaces in two different assemblies.
We made this fork to solve the problem, and we will pull in changes regularly from `arnoldasgudas` repository.

**Some features of MySql storage implementation is under development!**

## Installation

Install MySQL

Run the following command in the NuGet Package Manager console to install Hangfire.MySqlConnectorStorage:

```
Install-Package Hangfire.MySqlConnectorStorage
```

## Usage

Use one the following ways to initialize `MySqlStorage`: 
- Create new instance of `MySqlStorage` with connection string constructor parameter and pass it to `Configuration` with `UseStorage` method:
```
  GlobalConfiguration.Configuration.UseStorage(
    new MySqlStorage(connectionString));
```
- There must be `Allow User Variables` set to `true` in the connection string. For example: `server=127.0.0.1;uid=root;pwd=root;database={0};Allow User Variables=True`
- Alternatively one or more options can be passed as a parameter to `MySqlStorage`:
```
GlobalConfiguration.Configuration.UseStorage(
    new MySqlStorage(
        connectionString, 
        new MySqlStorageOptions
        {
            TransactionIsolationLevel = IsolationLevel.ReadCommitted,
            QueuePollInterval = TimeSpan.FromSeconds(15),
            JobExpirationCheckInterval = TimeSpan.FromHours(1),
            CountersAggregateInterval = TimeSpan.FromMinutes(5),
            PrepareSchemaIfNecessary = true,
            DashboardJobListLimit = 50000,
            TransactionTimeout = TimeSpan.FromMinutes(1),
        }));
```
Description of optional parameters:
- `TransactionIsolationLevel` - transaction isolation level. Default is read committed.
- `QueuePollInterval` - job queue polling interval. Default is 15 seconds.
- `JobExpirationCheckInterval` - job expiration check interval (manages expired records). Default is 1 hour.
- `CountersAggregateInterval` - interval to aggregate counter. Default is 5 minutes.
- `PrepareSchemaIfNecessary` - if set to `true`, it creates database tables. Default is `true`.
- `DashboardJobListLimit` - dashboard job list limit. Default is 50000.
- `TransactionTimeout` - transaction timeout. Default is 1 minute.

### How to limit number of open connections

Number of opened connections depends on Hangfire worker count. You can limit worker count by setting `WorkerCount` property value in `BackgroundJobServerOptions`:
```
app.UseHangfireServer(
   new BackgroundJobServerOptions
   {
      WorkerCount = 1
   });
```
More info: http://hangfire.io/features.html#concurrency-level-control

## Dashboard

Hangfire provides a dashboard
![Dashboard](https://camo.githubusercontent.com/f263ab4060a09e4375cc4197fb5bfe2afcacfc20/687474703a2f2f68616e67666972652e696f2f696d672f75692f64617368626f6172642d736d2e706e67)
More info: [Hangfire Overview](http://hangfire.io/overview.html#integrated-monitoring-ui)

## Build

Please use Visual Studio or any other tool of your choice to build the solution

## Test

In order to run unit tests and integrational tests set the following variables in you system environment variables (restart of Visual Studio is required):

`Hangfire_SqlServer_ConnectionStringTemplate` (default: `server=127.0.0.1;uid=root;pwd=root;database={0};Allow User Variables=True`)

`Hangfire_SqlServer_DatabaseName` (default: `Hangfire.MySql.Tests`)
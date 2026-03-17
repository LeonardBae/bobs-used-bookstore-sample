# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The solution has been transformed with no build errors across all projects:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

Since no build errors were detected, the transformation appears to have been successful. The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore and Build the Solution

Run the following commands from the root of the solution to confirm a clean restore and build:

```bash
dotnet restore
dotnet build
```

Ensure there are no warnings that could indicate compatibility issues, deprecated APIs, or missing references that were silently ignored during transformation.

---

## 2. Run the Domain Unit Tests

Execute the test suite in `Bookstore.Domain.Tests` to verify that core business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --verbosity normal
```

Review the test output carefully. Any failing tests should be investigated before proceeding further, as they may indicate behavioral regressions introduced during the transformation.

---

## 3. Verify Data Layer Functionality

The `Bookstore.Data` project likely contains database access logic such as Entity Framework Core migrations or repository implementations. Confirm the following:

- **Target framework**: Open `Bookstore.Data.csproj` and confirm it targets a supported .NET version (e.g., `net8.0`).
- **Database migrations**: If Entity Framework Core is used, verify that existing migrations are intact and apply cleanly:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj
```

- **Connection strings**: Confirm that connection strings in configuration files (e.g., `appsettings.json`) are correct for your target environment.

---

## 4. Validate the Web Project

Run the `Bookstore.Web` project locally to confirm it starts and serves requests correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj
```

Check the following:

- The application starts without runtime exceptions.
- Key routes and pages load correctly.
- Any authentication, authorization, or middleware configurations function as expected.
- Static assets are served properly.

---

## 5. Review the CDK Project

The `Bookstore.Cdk` project appears to be an AWS Cloud Development Kit (CDK) project used for infrastructure definition. Verify the following:

- The project targets a compatible .NET version.
- All CDK library NuGet packages (e.g., `Amazon.CDK.Lib`) are up to date and compatible with the target framework.
- Synthesize the CDK stack to confirm the infrastructure definition compiles correctly:

```bash
cd app/Bookstore.Cdk
cdk synth
```

If the `cdk` CLI is not installed, it can be installed via npm:

```bash
npm install -g aws-cdk
```

---

## 6. Check for Deprecated or Removed APIs

Even without build errors, some APIs available in .NET Framework may behave differently or have subtle incompatibilities in cross-platform .NET. Review the following areas:

- **Configuration**: Ensure `System.Configuration.ConfigurationManager` usage has been replaced with `Microsoft.Extensions.Configuration` where applicable.
- **HTTP clients**: Confirm `HttpClient` usage follows recommended patterns (e.g., using `IHttpClientFactory`).
- **Serialization**: If `Newtonsoft.Json` or `System.Text.Json` is used, verify serialization behavior is consistent with the original application.
- **File paths**: Ensure any hardcoded file paths use `Path.Combine` and are not Windows-specific.

---

## 7. Cross-Platform Smoke Test

If the intent is to run this application on Linux or macOS, perform a smoke test on the target platform:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj
```

Pay attention to any platform-specific runtime errors that would not surface on Windows.

---

## 8. Review NuGet Package Versions

Confirm that all NuGet packages across all projects are targeting stable, non-deprecated versions compatible with your chosen .NET version. You can check for outdated packages using:

```bash
dotnet list package --outdated
```

Update packages as needed, then re-run the build and tests to confirm stability.
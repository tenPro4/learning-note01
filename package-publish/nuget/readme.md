## Steps To Publish nuget package to Github

1. Build csproj. In fact, `dotnet pack` will execute build by default unless you add `--no-build`

2. Pack your project with command `dotnet pack` to generate `nupkg` source. It's recommended to add `-o` to specify the output path for easier file location in next step.
```bash
dotnet pack ./MyProject/MyProject.csproj -c Release /p:PackageVersion=xxx
```

3. Last, publish your package with `dotnet push` command. Replace {{my_username}} and {{my_pat}}
```bash
dotnet push "MyProject/bin/Release/*.nupkg" --source "https:nuget.pkg.github.com/{{my_username}}/index.json --api-key {{my_pat}}
```

Before execute `pack` command, need to define package metadata in your `.csproj` file
```xml
<PropertyGroup>
  <PackageId>MyProject</PackageId>
  <Version>1.0.0</Version>
  <RepositoryUrl>https://github.com/my_username/my_repo_name</RepositoryUrl>
  <RepositoryType>git</RepositoryType>
</PropertyGroup>
```

## Automation with Github Action

1. Create `.github/workflows/publish-nuget.yml` under root project
2. Define the automation action trigged when condition are meet
```yml
name: Publish NuGet Package

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

permissions:
  contents: read
  packages: write

env:
    PROJECT_NAME: "MyProject"
    PROJECT_PATH: "./MyProject/MyProject.csproj"
    DOTNET_VERSION: "8.0.x"

jobs:
  build-and-publish:
    runs-on: ubuntu-latest

    steps:
      # 1. Checkout
      - name: Checkout repository
        uses: actions/checkout@v3

      # 2. Setup .NET
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }}

      # 3. Restore dependencies
      - name: Restore dependencies
        run: dotnet restore

      # 4. Installxmllint
      - name: Install xmllint
        run: sudo apt-get update && sudo apt-get install -y libxml2-utils

      # 5. Use Sed to extract version
    #   - name: Get Package Version from csproj
    #     id: get_version
    #     shell: bash
    #     run: |
    #       VERSION=$(sed -n 's/.*<Version>\(.*\)<\/Version>.*/\1/p' ${{ env.PROJECT_PATH }} | head -n 1)
    #       echo "Detected Version: $VERSION"
    #       echo "VERSION=$VERSION" >> $GITHUB_ENV

      # 5. Get version from csproj by using xmllint
      - name: Get Package Version from csproj
        id: get_version
        run: |
          VERSION=$(xmllint --xpath "string(//Project/PropertyGroup/Version)" ${{ env.PROJECT_PATH }}
          echo "Package version from csproj: $VERSION"
          echo "VERSION=$VERSION" >> $GITHUB_ENV

      # 6. Pack NuGet package（Use the version define on csproj）
      - name: Pack NuGet package
        run: |
          dotnet pack ${{ env.PROJECT_PATH }} -c Release -o ./dist /p:PackageVersion=$VERSION

      # 7. Push to GitHub Packages
      - name: Push NuGet package
        run: |
          dotnet nuget push "dist/*.nupkg" \
            --source "https://nuget.pkg.github.com/${{ github.repository_owner }}/index.json" \
            --api-key ${{ secrets.GITHUB_TOKEN   }} \
            --skip-duplicate
```


## Install Nuget Package

You manually add new package source in `Visual Studio`

The recommended method is define the nuget.config in the root project
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
    <add key="github" value="https://nuget.pkg.github.com/tenPro4/index.json" />
  </packageSources>
  <packageSourceCredentials>
    <github>
      <add key="Username" value="tenPro4" />
      <add key="ClearTextPassword" value="%GITHUB_TOKEN%" />
    </github>
  </packageSourceCredentials>
</configuration>
```
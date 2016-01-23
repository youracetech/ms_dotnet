ms_dotnet Cookbook
==================
[![Cookbook Version][cookbook_version]][cookbook]
[![Build Status][build_status]][build_status]

Install the Microsoft .NET Framework.

Requirements
------------
This cookbook supports Chef 11.10.0+

### Platforms
* Windows XP
* Windows Vista
* Windows Server 2003 R2
* Windows 7
* Windows Server 2008 (R1, R2)
* Windows 8 and 8.1
* Windows Server 2012 (R1, R2)

### Cookbooks
The following cookbook is required as noted:

* [windows](windows_cookbook) (> 1.36.1)

    `ms_dotnet::default` include the recipe 'windows::default'
    `ms_dotnet::ms_dotnet2` and `ms_dotnet::ms_dotnet4` leverage the windows_package LWRP
    `ms_dotnet::ms_dotnet2`, `ms_dotnet::ms_dotnet3` and `ms_dotnet::ms_dotnet4` leverage the windows_feature LWRP
    `ms_dotnet_framework` LWRP leverage the windows_package LWRP

Known Issues
------------
Here are the known issues you can encounter with ms_dotnet recipes:
* "Access denied" error on windows_package when running chef via WinRM
  * `Cause`: winrm limitation
  * `Common environment`: knife windows bootstrap, chef-provisioning, test-kitchen
  * `Best solution`: your remoting system should try to simulate a local session (psexec or schedule task)
  * `Other solution`: create your custom wrapper to simulate a local session

Resource/Provider
-----------------
### ms_dotnet_framework
#### Actions
* `:install` - Install a specific .NET framework

#### Attribute Parameters
* `version` - Name attribute. Specify the .NET version to install.
* `include_patches` - Determine whether patches should also be applied (default: `true`)
* `feature_source` - Specify custom source for windows features. Only avaiable on NT Version 6.2 (Windows 8/2012) or newer. (default: `nil`)
* `package_sources` - Specify custom sources URL for windows packages. URL are indexed by their content SHA256 checkum.  (default: `{}`)
* `require_support` - Determine whether chef should fail when given version is not supported on the current OS (default: `false`)

> NB: `feature_source` works only on NT Version 6.2 (Windows 8/2012) or newer.

#### Examples
Install .NET 4.5.2 from custom sources

```ruby
ms_dotnet_framework '4.5.2' do
  action            :install
  include_patches   true
  package_sources   { 6c2c589132e830a185c5f40f82042bee3022e721a216680bd9b3995ba86f3781: 'http://my-own.site.com/NetFx452.exe' }
  require_support   true
end
```

Attributes
----------

#### ms_dotnet::ms_dotnet2
  * `node['ms_dotnet']['v2']['name']` - used to configure the Windows Package name
  * `node['ms_dotnet']['v2']['url']` - used to configure the source of the Windows Package
  * `node['ms_dotnet']['v2']['checksum']` - used to configure the checksum of the Windows Package

#### ms_dotnet::ms_dotnet3
  * `node['ms_dotnet']['v3']['source']` - used to configure the source of the Windows Package (only supported on NT Version 6.2 or newer)

#### ms_dotnet::ms_dotnet4
  * `node['ms_dotnet']['v4']['version']` - used to configure the desired version of .NET4 ('4.0', '4.5', '4.5.1', '4.5.2')
  * `node['ms_dotnet']['versions'][desired_version][feature]['name']` - used to configure the Feature name to use instead of a Windows Package for the specified `desired_version`
  * `node['ms_dotnet']['versions'][desired_version][package]['name']` - used to configure the Windows Package name for the specified `desired_version`
  * `node['ms_dotnet']['versions'][desired_version][package]['url']` - used to configure the source of the Windows Package for the specified `desired_version`
  * `node['ms_dotnet']['versions'][desired_version][package]['checksum']` - used to configure the checksum of the Windows Package for the specified `desired_version`
  * `node['ms_dotnet']['versions'][desired_version][patch]['name']` - used to configure the Windows Package name of the patch to apply for the specified `desired_version`
  * `node['ms_dotnet']['versions'][desired_version][patch]['url']` - used to configure the source of the Windows Package of the patch to apply for the specified `desired_version`
  * `node['ms_dotnet']['versions'][desired_version][patch]['checksum']` - used to configure the checksum of the Windows Package of the patch to apply for the specified `desired_version`

Libraries
---------
### Default
Provides convenients method for the `ms_dotnet` cookbook.

#### version_helper
Provides a factory to get specific VersionHelper instance.

### PackageHelper
References all official .NET setup & patches packages supported by this cookbook.

#### packages
Retrieve a Hash containing .NET setup packages info - `name`, `checksum`, `url` & `not_if` guard.

#### core?
Determine whether the current node is running on a Core version of Windows.

#### server?
Determine whether the current node is running on a Server version of Windows.

#### x64?
Determine whether the architecture of the current node is 64-bits.

### VersionHelper
Base abstract class inheriting from `PackageHelper` and providing convenient methods to determine which .NET version the current node supports, and how the setup should be handled.

#### features
Get windows features required by the given .NET version

#### installed_version
Get installed .NET version on the current node

#### package
Get windows package required by the given .NET version

#### patches
Get windows patches required by the given .NET version

#### supported_versions
Get all .NET versions supported on the current node OS

### V2Helper, V3Helper, V4Helper
Subclass of the `VersionHelper`, providing helpers for a specific major version of the .NET Framework.

Usage
-----

#### ms_dotnet::ms_dotnet2
To install Microsoft .NET Framework 2.0 on your node, just include the recipe `ms_dotnet::ms_dotnet2` in its `run_list`.
You can use a custom windows package by specifing the 3 attributes specified in the above section.

#### ms_dotnet::ms_dotnet3
To install Microsoft .NET Framework 3.0 on your node, just include the recipe `ms_dotnet::ms_dotnet3` in its `run_list`.

> NB: Starting with NT Version 6.2 (Windows 8/2012) .NET 3 is an _on demand feature_.
> Meaning that you need either to use the installing media or a custom windows image to enable the feature.
> See: http://msdn.microsoft.com/library/hh506443

#### ms_dotnet::ms_dotnet4
To install Microsoft .NET Framework 4 on your node, just include the recipe `ms_dotnet::ms_dotnet4` in its `run_list`.
Modify the version to install by changing the attribute `node['ms_dotnet']['v4']['version']`.

Contributing
------------
1. Fork the repository on Github
2. Create a named feature branch (like `add_component_x`)
3. Write your change
4. Write tests for your change (if applicable)
5. Run the tests, ensuring they all pass
6. Submit a Pull Request using Github

License and Authors
-------------------
Authors: [Baptiste Courtois][annih] (<b.courtois@criteo.com>), [Jeremy Mauro][jmauro] (<j.mauro@criteo.com>)

```text
Copyright 2014-2015, Criteo.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
[annih]:                    https://github.com/Annih
[jmauro]:                   https://github.com/jmauro
[repository]:               https://github.com/criteo-cookbooks/ms_dotnet
[powershell_cookbook]:      https://community.opscode.com/cookbooks/powershell
[build_status]:             https://api.travis-ci.org/criteo-cookbooks/ms_dotnet.svg?branch=master
[cookbook_version]:         https://img.shields.io/cookbook/v/ms_dotnet.svg
[cookbook]:                 https://supermarket.chef.io/cookbooks/ms_dotnet

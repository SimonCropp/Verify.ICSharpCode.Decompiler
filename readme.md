<!--
GENERATED FILE - DO NOT EDIT
This file was generated by [MarkdownSnippets](https://github.com/SimonCropp/MarkdownSnippets).
Source File: /readme.source.md
To change this file edit the source file and then run MarkdownSnippets.
-->

# <img src="/src/icon.png" height="30px"> Verify.ICSharpCode.Decompiler

[![Build status](https://ci.appveyor.com/api/projects/status/8kndmciqywvg350w?svg=true)](https://ci.appveyor.com/project/SimonCropp/verify-icsharpcode-decompiler)
[![NuGet Status](https://img.shields.io/nuget/v/Verify.ICSharpCode.Decompiler.svg)](https://www.nuget.org/packages/Verify.ICSharpCode.Decompiler/)

Extends [Verify](https://github.com/SimonCropp/Verify) to allow verification of assemblies via [ICSharpCode.Decompiler](https://github.com/icsharpcode/ILSpy/wiki/Getting-Started-With-ICSharpCode.Decompiler).

Support is available via a [Tidelift Subscription](https://tidelift.com/subscription/pkg/nuget-verify.icsharpcode.decompiler?utm_source=nuget-verify.icsharpcode.decompiler&utm_medium=referral&utm_campaign=enterprise).

<!-- toc -->
## Contents

  * [Usage](#usage)
    * [Verify Type](#verify-type)
    * [Verify Method](#verify-method)
  * [Security contact information](#security-contact-information)<!-- endtoc -->


## NuGet package

https://nuget.org/packages/Verify.ICSharpCode.Decompiler/


## Usage

Enable once at assembly load time:

<!-- snippet: Enable -->
<a id='snippet-enable'/></a>
```cs
VerifyICSharpCodeDecompiler.Enable();
```
<sup><a href='/src/Tests/GlobalSetup.cs#L9-L11' title='File snippet `enable` was extracted from'>snippet source</a> | <a href='#snippet-enable' title='Navigate to start of snippet `enable`'>anchor</a></sup>
<!-- endsnippet -->

Then given the following type:

<!-- snippet: Target.cs -->
<a id='snippet-Target.cs'/></a>
```cs
using System.ComponentModel;
using System.Runtime.CompilerServices;

public class Target :
    INotifyPropertyChanged
{
    void OnPropertyChanged([CallerMemberName] string? propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public event PropertyChangedEventHandler? PropertyChanged;

    string? property;

    public string? Property
    {
        get => property;
        set
        {
            property = value;
            OnPropertyChanged();
        }
    }
}
```
<sup><a href='/src/Tests/Target.cs#L1-L25' title='File snippet `Target.cs` was extracted from'>snippet source</a> | <a href='#snippet-Target.cs' title='Navigate to start of snippet `Target.cs`'>anchor</a></sup>
<!-- endsnippet -->


### Verify Type

<!-- snippet: TypeDefinitionUsage -->
<a id='snippet-typedefinitionusage'/></a>
```cs
[Fact]
public Task TypeDefinitionUsage()
{
    using var file = new PEFile(assemblyPath);
    var type = file.Metadata.TypeDefinitions
        .Single(x =>
        {
            var fullName = x.GetFullTypeName(file.Metadata);
            return fullName.Name == "Target";
        });
    return Verify(new TypeToDisassemble(file, type));
}
```
<sup><a href='/src/Tests/Tests.cs#L20-L33' title='File snippet `typedefinitionusage` was extracted from'>snippet source</a> | <a href='#snippet-typedefinitionusage' title='Navigate to start of snippet `typedefinitionusage`'>anchor</a></sup>
<!-- endsnippet -->

Result:

<!-- snippet: Tests.TypeDefinitionUsage.verified.txt -->
<a id='snippet-Tests.TypeDefinitionUsage.verified.txt'/></a>
```txt
.class public auto ansi beforefieldinit Target
	extends [System.Runtime]System.Object
	implements .custom instance void System.Runtime.CompilerServices.NullableAttribute::.ctor(uint8) = (
		01 00 00 00 00
	)
	[System.ObjectModel]System.ComponentModel.INotifyPropertyChanged
{
	.custom instance void System.Runtime.CompilerServices.NullableContextAttribute::.ctor(uint8) = (
		01 00 02 00 00
	)
	.custom instance void System.Runtime.CompilerServices.NullableAttribute::.ctor(uint8) = (
		01 00 00 00 00
	)
	// Fields
	.field private class [System.ObjectModel]System.ComponentModel.PropertyChangedEventHandler PropertyChanged
	.custom instance void [System.Runtime]System.Runtime.CompilerServices.CompilerGeneratedAttribute::.ctor() = (
		01 00 00 00
	)
	.custom instance void [System.Diagnostics.Debug]System.Diagnostics.DebuggerBrowsableAttribute::.ctor(valuetype [System.Diagnostics.Debug]System.Diagnostics.DebuggerBrowsableState) = (
		01 00 00 00 00 00 00 00
	)
	.field private string 'property'

	// Methods
	.method private hidebysig 
		instance void OnPropertyChanged (
			[opt] string propertyName
		) cil managed 
	{
		.param [1] = nullref
			.custom instance void [System.Runtime]System.Runtime.CompilerServices.CallerMemberNameAttribute::.ctor() = (
				01 00 00 00
			)
		// Method begins at RVA 0x209b
		// Code size 27 (0x1b)
		.maxstack 8

		IL_0000: nop
		IL_0001: ldarg.0
		IL_0002: ldfld class [System.ObjectModel]System.ComponentModel.PropertyChangedEventHandler Target::PropertyChanged
		IL_0007: dup
		IL_0008: brtrue.s IL_000d

		IL_000a: pop
		IL_000b: br.s IL_001a
...
```
<sup><a href='/src/Tests/Tests.TypeDefinitionUsage.verified.txt#L1-L46' title='File snippet `Tests.TypeDefinitionUsage.verified.txt` was extracted from'>snippet source</a> | <a href='#snippet-Tests.TypeDefinitionUsage.verified.txt' title='Navigate to start of snippet `Tests.TypeDefinitionUsage.verified.txt`'>anchor</a></sup>
<!-- endsnippet -->

A string for the type name can also be used:

<!-- snippet: TypeNameUsage -->
<a id='snippet-typenameusage'/></a>
```cs
[Fact]
public Task TypeNameUsage()
{
    using var file = new PEFile(assemblyPath);
    return Verify(new TypeToDisassemble(file, "Target"));
}
```
<sup><a href='/src/Tests/Tests.cs#L35-L42' title='File snippet `typenameusage` was extracted from'>snippet source</a> | <a href='#snippet-typenameusage' title='Navigate to start of snippet `typenameusage`'>anchor</a></sup>
<!-- endsnippet -->


### Verify Method

<!-- snippet: MethodNameUsage -->
<a id='snippet-methodnameusage'/></a>
```cs
[Fact]
public Task MethodNameUsage()
{
    using var file = new PEFile(assemblyPath);
    return Verify(
        new MethodToDisassemble(
            file,
            "Target",
            "OnPropertyChanged"));
}
```
<sup><a href='/src/Tests/Tests.cs#L44-L55' title='File snippet `methodnameusage` was extracted from'>snippet source</a> | <a href='#snippet-methodnameusage' title='Navigate to start of snippet `methodnameusage`'>anchor</a></sup>
<!-- endsnippet -->

Result:

<!-- snippet: Tests.MethodNameUsage.verified.txt -->
<a id='snippet-Tests.MethodNameUsage.verified.txt'/></a>
```txt
.method private hidebysig 
	instance void OnPropertyChanged (
		[opt] string propertyName
	) cil managed 
{
	.param [1] = nullref
		.custom instance void [System.Runtime]System.Runtime.CompilerServices.CallerMemberNameAttribute::.ctor() = (
			01 00 00 00
		)
	// Method begins at RVA 0x209b
	// Code size 27 (0x1b)
	.maxstack 8

	IL_0000: nop
	IL_0001: ldarg.0
	IL_0002: ldfld class [System.ObjectModel]System.ComponentModel.PropertyChangedEventHandler Target::PropertyChanged
	IL_0007: dup
	IL_0008: brtrue.s IL_000d

	IL_000a: pop
	IL_000b: br.s IL_001a

	IL_000d: ldarg.0
	IL_000e: ldarg.1
	IL_000f: newobj instance void [System.ObjectModel]System.ComponentModel.PropertyChangedEventArgs::.ctor(string)
	IL_0014: callvirt instance void [System.ObjectModel]System.ComponentModel.PropertyChangedEventHandler::Invoke(object, class [System.ObjectModel]System.ComponentModel.PropertyChangedEventArgs)
	IL_0019: nop

	IL_001a: ret
} // end of method Target::OnPropertyChanged
...
```
<sup><a href='/src/Tests/Tests.MethodNameUsage.verified.txt#L1-L31' title='File snippet `Tests.MethodNameUsage.verified.txt` was extracted from'>snippet source</a> | <a href='#snippet-Tests.MethodNameUsage.verified.txt' title='Navigate to start of snippet `Tests.MethodNameUsage.verified.txt`'>anchor</a></sup>
<!-- endsnippet -->


## Security contact information

To report a security vulnerability, use the [Tidelift security contact](https://tidelift.com/security). Tidelift will coordinate the fix and disclosure.


## Icon

[Gem](https://thenounproject.com/term/shatter/1084820/) designed by [Bakunetsu Kaito](https://thenounproject.com/sevenknights_friendship/) from [The Noun Project](https://thenounproject.com/creativepriyanka).
# Dotnet-XrmToolBox-Plugin

XrmToolBox Plugin Projectを作る手順

---

# [Create your own tool for XrmToolBox](https://www.xrmtoolbox.com/documentation/for-developers/create-your-own-plugin-for-xrmtoolbox/)

## [Install XrmToolBox Plugin Project Template](https://www.xrmtoolbox.com/documentation/for-developers/install-xrmtoolbox-plugin-project-template/)

1. [XrmToolBox Tool Project Template - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=TanguyTMVPCRM.XrmToolBoxPluginProjectTemplate-19499) の `Download` をクリックして、 `XrmToolBox_PluginTemplate.vsix` をダウンロード
2. `XrmToolBox_PluginTemplate.vsix` をダブルクリックするとダイアログが開くので、 `Install` をクリックし、「インストールが完了」と表示されたら `Close` をクリック

## [Create your first tool for XrmToolBox](https://www.xrmtoolbox.com/documentation/for-developers/create-your-own-plugin-for-xrmtoolbox/#create)

1. Visual Studioを開き、 `XrmToolBox Plugin Project Template` を利用して新しいプロジェクトを作成（.NET Frameworkのバージョンは `4.6.2`）
2. ツール グラフィック チャーターをカスタマイズするために `MyPlugin.cs` を編集

```cs
    // To generate Base64 string for Images below, you can use https://www.base64-image.de/
    [Export(typeof(IXrmToolBoxPlugin)),
        ExportMetadata("Name", "My First Plugin"),
        ExportMetadata("Description", "This is a description for my first plugin"),
        // Please specify the base64 content of a 32x32 pixels image
        ExportMetadata("SmallImageBase64", null),
        // Please specify the base64 content of a 80x80 pixels image
        ExportMetadata("BigImageBase64", null),
        ExportMetadata("BackgroundColor", "Lavender"),
        ExportMetadata("PrimaryFontColor", "Black"),
        ExportMetadata("SecondaryFontColor", "Gray")]
```

| プロパティ         | 内容                                                                         |
| ------------------ | ---------------------------------------------------------------------------- |
| Name               | ツールリストに表示される名前                                                 |
| Description        | ツールリストに表示される簡単な説明                                           |
| SmallImageBase64   | 縦横32ピクセルの画像を表すbase64文字列                                       |
| BigImageBase64     | 縦横80ピクセルの画像を表すbase64文字列                                       |
| BackgroundColor    | ツールリストのツールアイテムの背景色                                         |
| PrimaryFontColor   | 名前、説明、バージョン（およびラージアイコン表示モードの作成者）のフォント色 |
| SecondaryFontColor | スモールアイコン表示モードの作成者のフォント色                               |

3. ツールの作成者とバージョンを定義するために `AssemblyInfo.cs` を編集

```cs
[assembly: AssemblyTitle("MyXrmToolBoxTool1")]
[assembly: AssemblyDescription("")]
[assembly: AssemblyConfiguration("")]
[assembly: AssemblyCompany("")]
[assembly: AssemblyProduct("MyXrmToolBoxTool1")]
[assembly: AssemblyCopyright("Copyright ©  2023")]
[assembly: AssemblyTrademark("")]
[assembly: AssemblyCulture("")]
```

4. デザイナーモードでメインコントロール（ `MyPluginControl.cs` ）を開くと、 `PluginIcon` と `TrayIcon` のプロパティからアイコンを追加可能

5. `MyPluginControl.cs` にカスタムロジックを記述。
  - 使える機能は [このページ](https://www.xrmtoolbox.com/documentation/for-developers/plugincontrolbase-base-class/) で確認
  - 事前バインド型エンティティクラスは使ってはいけない

6. サードパーティ製のライブラリを利用する場合は、 [ILMerge](https://www.nuget.org/packages/MSBuild.ILMerge.Task/) を使ってツールとその依存関係を1つのファイルにまとめる

## [Add a settings file](https://www.xrmtoolbox.com/documentation/for-developers/add-a-settings-file/)

1. `Settings` クラスを追加（XrmToolBox Plugin Project Templateには既定で存在）

```cs
public class Settings
{
    public string Var1 { get; set; }
    public bool Var2 { get; set; }
}
```

2. 読み書き

```cs
if (!SettingsManager.Instance.TryLoad(GetType(), out mySettings))
{
    mySettings = new Settings();
}

LogInfo("LastUsedOrganizationWebappUrl: {0}", mySettings.LastUsedOrganizationWebappUrl);

SettingsManager.Instance.Save(GetType(), mySettings);
SettingsManager.Instance.Save(typeof(SampleTool), mySettings, ConnectionDetail.ConnectionId.ToString());
```

## [Debug your tool](https://www.xrmtoolbox.com/documentation/for-developers/debug/)

1. ビルドイベント設定の `post-build event` を定義

```powershell
IF $(ConfigurationName) == Debug (
      IF NOT EXIST Plugins mkdir Plugins
      xcopy "$(TargetDir)$(TargetFileName)" "$(TargetDir)Plugins\" /Y
      xcopy "$(TargetDir)$(TargetName).pdb" "$(TargetDir)Plugins\" /Y
      )

# IF NOT EXIST Plugins mkdir Plugins
# move /Y $(TargetFileName) Plugins
```

2. デバッグ設定の `external program` `Command line arguments` を定義

```
# external program
...\bin\Debug\XrmToolBox.exe

# Command line arguments
/overridepath:.
```

## [Deploy your tool in Tool Library](https://www.xrmtoolbox.com/documentation/for-developers/deploy-your-plugin-in-plugins-store/)

1.

---

Copyright (c) 2023 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.

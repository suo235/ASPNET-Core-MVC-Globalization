# ASPNET-Core-MVC-Globalization
Repository that you should refer when you have trouble globalizing your ASP.NET Core MVC application. / ASP.NET Core MVC アプリケーションの多言語化で困った際に見るべきリポジトリ

## What happended / 何が起きたか
Your web page does NOT display the desired language although you change the language setting of your browser.

ブラウザの表示言語を変更してもWebページの表示言語が期待する言語にならない。

## What needed / 必要なもの
```cs
builder.Services.AddLocalization(options => options.ResourcesPath = "Resources");

builder.Services.AddMvc()
.AddViewLocalization(LanguageViewLocationExpanderFormat.Suffix)
.AddDataAnnotationsLocalization();

builder.Services.Configure<RequestLocalizationOptions>(options =>
{
    // For browsers that includes neutral languages (ex: en) into Accept-Language header
    // Accept-Languageにニュートラル言語(jaなど)を入れてくるブラウザへの対応
    var supportedCultures = new[] { "ja", "en" };

    // DO NOT USE THIS
    // var supportedCultures = new[] { "ja-JP", "en-US" };

    options.SetDefaultCulture(supportedCultures[0])
        .AddSupportedCultures(supportedCultures)
        .AddSupportedUICultures(supportedCultures);
});

var app = builder.Build();

var localizationOptions = app.Services.GetService<IOptions<RequestLocalizationOptions>>();
localizationOptions!.Value.ApplyCurrentCultureToResponseHeaders = true;
// These lines are needed to set the language to "en" when a query string with "/?culture=en-US" is passed to the page
// "ja-JP" を "ja" にフォールバックするための記述
localizationOptions!.Value.FallBackToParentCultures = true;
localizationOptions!.Value.FallBackToParentUICultures = true;
app.UseRequestLocalization(localizationOptions!.Value);
```

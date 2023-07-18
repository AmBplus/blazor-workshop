# کامپوننت ها (Components) و لایوت (layout)  

در این بخش ، ما شروع میکنیم به ساختن یک برنامه فروش پیتزا با استفاده از بلیزور . این برنامه به کاربران اجازه میده که بتونن پیتزا سفارش بدن ، اون رو سفارشی کنن و بعد سفارششون رو رهگیری کنن .
## نقطه شروع پیتزا فروشی  

ما یک سولوشن(solution) ابتدایی برنامه پیتزا فروشی برای شما در این ریپوزیتوری(repo) آماده کردیم . برید و این ریپوزیتوری رو روی دستگاه خودتون کلون(clone) کنید .بعد میتونید وارد بخش شروع ([starting point](https://github.com/dotnet-presentations/blazor-workshop/tree/master/save-points/00-get-started)) بشید ، در این فولدر شما میتونید با توجه هر بخش که مطالعه میکنید کد های همون بخش رو ببینید که به اصطلاح برای شما (*save-points*) صورت گرفته .
> نکته : اگر شما کد های این ورکشاپ رو به جای دیگه دستگاهتون (سیستمتون) انتقال میدید ، مطمئن بشید که  فایل *Directory.Build.props* در روت(root) این ریپوزیتوری قرار داره رو کپی می کنین که برای بازگردانی ورژن درست پکیج ها صورت میگیره .
 
< در صورتی که در ساخت و ایجاد (build) پروژه به ارور میخورید ، فایل های obj , bin را حذف کنید ، سپس دوباره پروژه را بیلد کنید .

این سولوشن در حال حاظر شامل چهار پروژه میشه : 

![image](https://user-images.githubusercontent.com/1874516/77238114-e2072780-6b8a-11ea-8e44-de6d7910183e.png)


- **بلیزینگ پیترا کلاینت(BlazingPizza.Client)**: یک پروژه بلیزور شامل کامپوننت های ui برای برنامه هست . 
- **بلیزینگ پیتزا سرور(BlazingPizza.Server)**: این یک پروژه asp.net core برای هاست کردن برنامه بلیزور و همچنین بک اند سرویس های برنامه هست.
- **بلیزینگ پیتزا مشترک(BlazingPizza.Shared)**: این پروژه شامل مدل های به اشتراک گذاشته شده برای برنامست.
- **بلیزینگ پیتزا کتابخانه کامپوننت ها (BlazingPizza.ComponentsLibrary)**: این کتابخانه شامل کامپوننت ها و کد های کمک کننده برای استفاده در بخش های بعدیست.

 **بلیزینگ پیتزا سرور (BlazingPizza.Server)** : این پروژه باید به عنوان یک پروژه شروع ست(قرار بگیره) بشه


وقتی که برنامه را اجرا کردید ، شما میتونید ببینید که در حال حاظر فقط شامل یک صفحه اصلی(Home Page) سادست .

![image](https://user-images.githubusercontent.com/1874516/77238160-25fa2c80-6b8b-11ea-8145-e163a9f743fe.png)

آدرس رو به رو رو باز کنید *Pages/Index.razor* در بخش **BlazingPizza.Client** شما میتونید کد های صفحه اصلی رو ببینید.

```
@page "/"

<h1>Blazing Pizzas</h1>
```
این صفحه اصلی به عنوان یک کامپوننت تک قرار گرفته . دستور `page@` مشخص میکنه که `Index` کامپوننت یک صفحه صفحه قابل ردیابی با مسیر مشخصه .



## نشان دادن لیست پیتزا های ویژه

ابتدا صفحه اصلی را به روز می کنیم تا لیستی از پیتزاهای ویژه موجود را نمایش بده . فهرست ویژه‌ ها بخشی از وضعیت کامپوننت `Index` خواهد بود.

یک بلوک `code@` به *Index.razor* اضافه کنید به همراه یک فیلد نوع لیست پیتزاهای ویژه تا بتونید موارد ویژه موجود را پیگیری کنید:
```csharp
@code {
    List<PizzaSpecial> specials;
}
```

کد موجود در بلوک `@code` به کلاس تولید شده برای کامپوننت(مولفه) اضافه می شود. نوع `PizzaSpecial` قبلاً برای شما در پروژه **BlazingPizza.Shared** تعریف شده است.

برای دریافت لیست موجود از موارد ویژه، باید یک API در بک اند(backend) فراخوانی کنیم. Blazor یک `HttpClient` از پیش تنظیم شده را از طریق تزریق وابستگی ارائه می ده که از قبل با آدرس پایه درست تنظیم شده است. از دستورالعمل `inject@` برای تزریق `HttpClient` به کامپوننت(مؤلفه) `Index` استفاده کنید.
```
@page "/"
@inject HttpClient HttpClient
```

دستورالعمل 'inject@' اساساً یک ویژگی جدید را بر روی کامپوننت(مؤلفه) تعریف می کند که در آن توکن اول نوع پروپرتی(property) را مشخص می کند و توکن دوم نام پروپرتی را مشخص می کند. پروپرتی با استفاده از تزریق وابستگی از قبل برای شما پر شده است.

برای بازیابی لیست پیتزاهای ویژه، متد(method) `OnInitializedAsync` را در بلوک `@code` اورراید(overide) کنید. این روش بخشی از چرخه حیات کامپوننت است و زمانی فراخوانی می شود که کامپوننت مقدار دهی اولیه شود. از متد `()<GetFromJsonAsync<T>` برای مدیریت دسریالایز(deserializing) پاسخ JSON استفاده کنید:
```csharp
@code {
    List<PizzaSpecial> specials;

    protected override async Task OnInitializedAsync()
    {
        specials = await HttpClient.GetFromJsonAsync<List<PizzaSpecial>>("specials", BlazingPizza.OrderContext.Default.ListPizzaSpecial);
    }
}
```


آدرس "specials/" ای پی ای(API) های تعریف شده به صورت مینیمال ای پی ای(minimal API) است که در فایل `PizzaApiExtensions.cs` داخل پروژه **BlazingPizza.Server** قرار گرفته است .

همچنین درمورد اطلاعات بیشتر درمورد مینیمال ای پی ای ها میتونید به آدرس [Create a minimal web API with ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/tutorials/min-web-api?view=aspnetcore-6.0) مراجعه کنید .


> توجه: `BlazingPizza.OrderContext.Default.ListPizzaSpecial` به سریال سازی(serialization) Json با [مولدهای منبع(source generators)] اشاره دارد که میتونید در لینک (https://docs.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-source-generation) مشاهده بفرمایید .
 
Once the component is initialized it will render its markup. Replace the markup in the `Index` component with the following to list the pizza specials:


```html
<div class="main">
    <ul class="pizza-cards">
        @if (specials != null)
        {
            @foreach (var special in specials)
            {
                <li style="background-image: url('@special.ImageUrl')">
                    <div class="pizza-info">
                        <span class="title">@special.Name</span>
                        @special.Description
                        <span class="price">@special.GetFormattedBasePrice()</span>
                    </div>
                </li>
            }
        }
    </ul>
</div>
```

Run the app by hitting `Ctrl-F5`. Now you should see a list of the specials that are available.

![image](https://user-images.githubusercontent.com/1874516/77239386-6c558880-6b97-11ea-9a14-83933146ba68.png)


## Create the layout

Next we'll set up the layout for the app. 

Layouts in Blazor are also components. They inherit from `LayoutComponentBase`, which defines a `Body` property that can be used to specify where the body of the layout should be rendered. The layout component for our pizza store app is defined in *Shared/MainLayout.razor*.

```html
@inherits LayoutComponentBase

<div class="content">
    @Body
</div>
```

To see how the layout is associated with your pages, look at the `<Router>` component in `App.razor`. Notice that the `DefaultLayout` parameter determines the layout used for any page that doesn't specify its own layout directly.

You can also override this `DefaultLayout` on a per-page basis. To do so, you can add a directive such as `@layout SomeOtherLayout` at the top of any `.razor` page component. However, you will not need to do so in this application.

Update the `MainLayout` component to define a top bar with a branding logo and a nav link for the home page:

```html
@inherits LayoutComponentBase

<div class="top-bar">
    <a class="logo" href="">
        <img src="img/logo.svg" />
    </a>

    <NavLink href="" class="nav-tab" Match="NavLinkMatch.All">
        <img src="img/pizza-slice.svg" />
        <div>Get Pizza</div>
    </NavLink>
</div>

<div class="content">
    @Body
</div>
```

کامپوننت `NavLink` توسط Blazor ارائه شده است. کامپوننت‌ها را می‌توان با تعیین یک عنصر با نام نوع مؤلفه(کامپوننت) به همراه ویژگی‌های(attributes) پارامتر های کامپوننت استفاده کرد. به طور مثلا کامپوننت (NavLink) به صورت زیر میتونیم استفاده کنیم 
```csharp
<NavLink><NavLink/>
```
کامپوننت `NavLink` مانند یک انکِرتَگ(anchor tag) است، با این تفاوت که اگر URL فعلی با آدرس پیوند مطابقت داشته باشد، یک کلاس `اکتیو` اضافه می کند. 'NavLinkMatch.All' به این معنی است که پیوند فقط زمانی باید فعال باشد که با کل URL فعلی (نه فقط یک پیشوند) مطابقت داشته باشد. در جلسه بعد مؤلفه NavLink را با جزئیات بیشتری بررسی خواهیم کرد.
اگر از ویژوال استدویو (Visual Studio) استفاده میکنین میتوانید با `Ctrl-F5` برنامه رو اجرا کنید ، در غیر این صورت با دستور ÷÷ در کامند لاین رو اجرا کنید ، با لایوت(layout) جدیدمون ، برنامه پیتزا فروشی ما الان شبیه زیر شده :

![image](https://user-images.githubusercontent.com/1874516/77239419-aa52ac80-6b97-11ea-84ae-f880db776f5c.png)


بخش بعد - [سفارشی سازی پیتزا](02-customize-a-pizza.md)

---
title: "委托和 lambda"
description: "委托和 lambda"
keywords: ".NET、.NET Core"
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: fe2e4b4c-6483-4106-a4b4-a33e2e306591
translationtype: Human Translation
ms.sourcegitcommit: 9cf6022fc910bc5418c03c0fa81d9432d85be3b0
ms.openlocfilehash: 7e9bb11db4b3586639a0447737db9cd376898325

---

# <a name="delegates-and-lambdas"></a>委托和 lambda

委托定义类型，类型指定特定方法签名。 可将满足此签名的方法（静态或实例）分配给该类型的变量，然后（使用适当参数）直接调用该方法，或将其作为参数本身传递给另一方法再进行调用。 以下示例演示了委托的用法。

```cs
public class Program
{

  public delegate string Reverse(string s);

  static string ReverseString(string s)
  {
      return new string(s.Reverse().ToArray());
  }

  static void Main(string[] args)
  {
      Reverse rev = ReverseString;

      Console.WriteLine(rev("a string"));
  }
}

```

*   在第 4 行，创建特定签名的委托类型，在本例中即接受字符串参数并返回字符串参数的方法。
*   在第 6 行，提供具有完全相同的签名的方法，以定义委托的实施。
*   在第 13 行，将该方法分配给符合 `Reverse` 委托的类型。
*   最后，在第 15 行，传递要反转的字符串来调用该委托。

为简化开发过程，.NET 包含一组委托类型，程序员可重用这些类型而无需创建新类型。 其中包括 `Func<>`、`Action<>` 和 `Predicate<>`，可用于 .NET API 的各个位置，无需定义新委托类型。 当然，从这三者的签名可以看出它们之间存在某些差异，主要影响其既定用途：

*   `Action<>` 用于需要使用委托参数执行操作的情况。
*   `Func<>` 通常用于现有转换的情况，也就是说需要将委托参数转换为其他结果时。 最好的示例就是投影。
*   `Predicate<>` 用于需要确定参数是否满足委托条件的情况。 也可将其写作 `Func<T, bool>`。

现在可使用 `Func<>` 委托而非自定义类型重新编写上述示例。 程序将照旧继续运行。

```cs
public class Program
{

  static string ReverseString(string s)
  {
      return new string(s.Reverse().ToArray());
  }

  static void Main(string[] args)
  {
      Func<string, string> rev = ReverseString;

      Console.WriteLine(rev("a string"));
  }
}

```

对于这个简单的示例而言，在 Main() 方法外定义方法似乎有些多余。 因此 .NET Framework 2.0 引入了**匿名委托**的概念。 在其支持下，可以创建“内联”委托，而无需指定任何其他类型或方法。 只需在所需位置内联委托的定义即可。

例如，要进行切换并使用匿名委托筛选出只有偶数的列表，然后将其打印到控制台。

```cs
public class Program
{

  public static void Main(string[] args)
  {
    List<int> list = new List<int>();

    for (int i = 1; i <= 100; i++)
    {
        list.Add(i);
    }

    List<int> result = list.FindAll(
      delegate(int no)
      {
          return (no%2 == 0);
      }
    );

    foreach (var item in result)
    {
        Console.WriteLine(item);
    }
  }
}

```

请注意突出显示的行。 如你所见，该委托的正文只是一组表达式，其他所有委托也是如此。 但它并非单独定义，而是临时引入对 `List<T>` 类型 `FindAll()` 方法的调用的。

但是，即使使用此方法，仍有许多可以丢弃的代码。 此时就需要使用 **lambda 表达式**。

lambda 表达式（或简称“lambda”）是在 C# 3.0 中作为语言集成查询的 (LINQ) 核心构建基块首次引入的。 这种表达式只是使用委托的更方便的语法。 它们将声明签名和方法正文，但在分配到委托之前没有自己的正式标识。 与委托不同，可将其作为事件注册的左侧或在各种 Linq 子句和方法中直接分配。

由于 lambda 表达式只是指定委托的另一种方式，因此应可重新编写上述示例，令其使用 lambda 表达式而不是匿名委托。

```cs
public class Program
{

  public static void Main(string[] args)
  {
    List<int> list = new List<int>();

    for (int i = 1; i <= 100; i++)
    {
        list.Add(i);
    }

    List<int> result = list.FindAll(i => i % 2 == 0);

    foreach (var item in result)
    {
        Console.WriteLine(item);
    }
  }
}

```

查看突出显示的行，即可了解 lambda 表达式的外观。 再次强调，它只是使用委托的一种**非常**方便的语法，因此其实际行为与使用匿名委托时相同。

再次强调，lambda 只是委托，这意味着可将其顺利用作事件处理程序，如以下代码片段所示。

```cs
public MainWindow()
{
    InitializeComponent();

    Loaded += (o, e) =>
    {
        this.Title = "Loaded";
    };
}

```

## <a name="further-reading-and-resources"></a>其他阅读材料和资源

*   [委托](https://msdn.microsoft.com/library/ms173171.aspx)
*   [匿名函数](https://msdn.microsoft.com/library/bb882516.aspx)
*   [Lambda 表达式](https://msdn.microsoft.com/library/bb397687.aspx)



<!--HONumber=Nov16_HO3-->



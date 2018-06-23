
# Agricultural Approaches to Data Science - F# and the World Bank Data API
##### David Miller - June 2018
---

As a nod to this piece having some sort of back story, it's probably worth telling you that my Dad was a farmer. I grew up in rural England and spent much of my childhood covered in various flavours of mud, dust, and other less desirable substances. The upshot of this (and the amount of Stargate, Voyager, and TNG I was watching at the time) means that I now have a penchant for hydroponics, a slightly unhealthy fascination with fertilizer, and a lasting interest in all things agricultural. 

So you can imagine my joy when, as I was perusing through the the World Bank dataset, I found a shed load of indicators to play with. I'm going to do my best to gather various data, manipulate it into a respectable format (sometimes easier said than done), and squeeze it until it tells me something interesting. 

## This Blog

While I've been using FSharp and Jupyter separately for a while, this is my first outing with an IFSharp Jupyter notebook, and I'm mostly here to see what's possible. Either way, I suspect I'm going to learn something interesting from this, and I hope you will too.

## Accessing the World Bank Data API
I think anyone who's searched for "fsharp *something* example" has probably seen something approaching the next few lines of code, so I'll not dwell on them too long. Somewhere along the way, the great people who maintain the [FSharp.Data](http://fsharp.github.io/FSharp.Data/) library, saw fit to add a type provider to the World Bank's open dataset API, and, well, it's great. With a few lines of code, you can gather economic, demographic, geographic or geological data series for almost any country you like. If you have a chance to visit their [website](https://data.worldbank.org/) to look around, you will be well rewarded.
    
The array of fields available is so vastly, hugely, mind-bogglingly big, that you will likely not know where to start. Just like me. 

It turns out though, after a swift Google, that this is probably your best bet:


```fsharp
#load "Paket.fsx"
Paket.Package ["FSharp.Data"]
#load "Paket.Generated.Refs.fsx"
```


```fsharp
open FSharp.Data
```


```fsharp
let wb = WorldBankData.GetDataContext()
```

Good stuff. The code has run without any issues and we've got an object to fiddle with.

### Take a look in the mirror
The API has some [documentation](http://fsharp.github.io/FSharp.Data/library/WorldBank.html), but as with most open source libraries, you're largely left on your own to figure out what's what from limited examples or old blogposts. With this in mind there are a couple of options available to us. 

First and foremost is **reflection**, which, if you've heard of it already then bully for you, but if not, is a library that allows you to access and manipulate the metadata of your code's types and classes at runtime. In certain cases it can be incredibly useful, but it also looks a lot like a silver bullet for many programming problems, and as such can be used and abused in horrible ways. 

For now though, I'm going to call 3 simple methods, available on any type or class, to tell me more about them:

*An obligatory note from someone who's had to deal with applications that use it to access properties "dynamically"; Stop it, right now - it might seem like fun, but think about a) the performance costs when you have to scale up to bigger datasets and b) the poor sod who then has to 'fix' it.*


```fsharp
wb.GetType()
```




    FSharp.Data.Runtime.WorldBank.WorldBankData




```fsharp
wb.GetType().GetProperties()
    |> Util.Table
```




<table><thead><tr><th>MemberType</th><th>PropertyType</th><th>Attributes</th><th>CanRead</th><th>CanWrite</th><th>GetMethod</th><th>SetMethod</th><th>IsSpecialName</th><th>Name</th><th>DeclaringType</th><th>ReflectedType</th><th>CustomAttributes</th><th>MetadataToken</th><th>Module</th></tr></thead><tbody><tbody></tbody></table>




```fsharp
wb.GetType().GetMethods()
    |> Util.Table
```




<table><thead><tr><th>MemberType</th><th>ReturnType</th><th>ReturnParameter</th><th>ReturnTypeCustomAttributes</th><th>MethodImplementationFlags</th><th>MethodHandle</th><th>Attributes</th><th>CallingConvention</th><th>IsGenericMethodDefinition</th><th>ContainsGenericParameters</th><th>IsGenericMethod</th><th>IsSecurityCritical</th><th>IsSecuritySafeCritical</th><th>IsSecurityTransparent</th><th>IsPublic</th><th>IsPrivate</th><th>IsFamily</th><th>IsAssembly</th><th>IsFamilyAndAssembly</th><th>IsFamilyOrAssembly</th><th>IsStatic</th><th>IsFinal</th><th>IsVirtual</th><th>IsHideBySig</th><th>IsAbstract</th><th>IsSpecialName</th><th>IsConstructor</th><th>Name</th><th>DeclaringType</th><th>ReflectedType</th><th>CustomAttributes</th><th>MetadataToken</th><th>Module</th></tr></thead><tbody><tr><td>Method</td><td>System.String</td><td>System.String </td><td>System.String </td><td>IL</td><td>System.RuntimeMethodHandle</td><td>PrivateScope, Public, Virtual, HideBySig, VtableLayoutMask</td><td>Standard, HasThis</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td><td>False</td><td>ToString</td><td>System.Object</td><td>FSharp.Data.Runtime.WorldBank.WorldBankData</td><td>System.Collections.ObjectModel.ReadOnlyCollection`1[System.Reflection.CustomAttributeData]</td><td>100663841</td><td>CommonLanguageRuntimeLibrary</td></tr><tr><td>Method</td><td>System.Boolean</td><td>Boolean </td><td>Boolean </td><td>IL</td><td>System.RuntimeMethodHandle</td><td>PrivateScope, Public, Virtual, HideBySig, VtableLayoutMask</td><td>Standard, HasThis</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td><td>False</td><td>Equals</td><td>System.Object</td><td>FSharp.Data.Runtime.WorldBank.WorldBankData</td><td>System.Collections.ObjectModel.ReadOnlyCollection`1[System.Reflection.CustomAttributeData]</td><td>100663842</td><td>CommonLanguageRuntimeLibrary</td></tr><tr><td>Method</td><td>System.Int32</td><td>Int32 </td><td>Int32 </td><td>IL</td><td>System.RuntimeMethodHandle</td><td>PrivateScope, Public, Virtual, HideBySig, VtableLayoutMask</td><td>Standard, HasThis</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>False</td><td>False</td><td>GetHashCode</td><td>System.Object</td><td>FSharp.Data.Runtime.WorldBank.WorldBankData</td><td>System.Collections.ObjectModel.ReadOnlyCollection`1[System.Reflection.CustomAttributeData]</td><td>100663845</td><td>CommonLanguageRuntimeLibrary</td></tr><tr><td>Method</td><td>System.Type</td><td>System.Type </td><td>System.Type </td><td>InternalCall</td><td>System.RuntimeMethodHandle</td><td>PrivateScope, Public, HideBySig</td><td>Standard, HasThis</td><td>False</td><td>False</td><td>False</td><td>True</td><td>True</td><td>False</td><td>True</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>False</td><td>True</td><td>False</td><td>False</td><td>False</td><td>GetType</td><td>System.Object</td><td>FSharp.Data.Runtime.WorldBank.WorldBankData</td><td>System.Collections.ObjectModel.ReadOnlyCollection`1[System.Reflection.CustomAttributeData]</td><td>100663846</td><td>CommonLanguageRuntimeLibrary</td></tr><tbody></tbody></table>



Huh... 

So these three methods should have taken the results of our `WorldBank.GetDataContext()` and told me the name of the type that was returned...
> FSharp.Data.Runtime.WorldBank.WorldBankData

... good, but also a list of the public properties and the methods of that type, i.e. ways in which I can access the data, of which there are none.

Hmmm... So much for Reflection, time to try something else.

### Use the Source, Luke
So the outputs from the last attempt didn't seem so helpful, but, at the very least, it has told me where to look. My targeting computer's off and I'm going to use my gut.

A small amount of hunting around the FSharp.Data github site yields a promisingly named 'WorldBank' folder, and within it, two helpful looking files:

* https://github.com/fsharp/FSharp.Data/blob/master/src/WorldBank/WorldBankProvider.fs
* https://github.com/fsharp/FSharp.Data/blob/master/src/WorldBank/WorldBankRuntime.fs

Bingo. It's usually not worth digging around in the source code, but sometimes you're not left with much of a choice. I'm guessing from the fact that the `WorldBank.WorldBankData` class was a part of the `FSharp.Data.Runtime` namespace that the **WorldBankRuntime.fs** file is the place to look.



And we have it:

```fsharp
/// [omit]
type IWorldBankData =
    abstract GetCountries<'T when 'T :> Country> : unit -> seq<'T>
    abstract GetRegions<'T when 'T :> Region> : unit -> seq<'T>
    abstract GetTopics<'T when 'T :> Topic> : unit -> seq<'T>

/// [omit]
type WorldBankData(serviceUrl:string, sources:string) = 
    let sources = sources.Split([| ';' |], StringSplitOptions.RemoveEmptyEntries) |> Array.toList
    let restCache = createInternetFileCache "WorldBankRuntime" (TimeSpan.FromDays 30.0)
    let connection = new ServiceConnection(restCache, serviceUrl, sources)
    interface IWorldBankData with
        member x.GetCountries() = CountryCollection(connection, None) :> seq<_>
        member x.GetRegions() = RegionCollection(connection) :> seq<_>
        member x.GetTopics() = TopicCollection(connection) :> seq<_>
```

Right at the bottom we have the `WorldBankData` class and the `IWorldBankData` interface it inherits from. For now the interface is irrelevant, but if you're interested in coding for applications rather than just for data analysis, I recommend reading about [them](https://fsharpforfunandprofit.com/posts/interfaces/) and [why they're useful](https://en.wikipedia.org/wiki/Object-oriented_programming).

The class itself has 3 member methods that I'm interested in `GetCountries()`, `GetRegions()` and `GetTopics()`. Looking at the names used it looks rather like each generates a collection of objects from a connection to the WorldBank database. 

Let's find out:


```fsharp
let topics = wb.GetTopics()
```

    The field, constructor or member 'GetTopics' is not defined. Maybe you want one of the following:
       get_Topics
       GetType
       Topics

**An error?** Hmmm... curious, but at least IFSharp knows where I'm supposed to be looking; `Topics` or `get_Topics`

To recap we looked at the type output by the `WorldBankData.GetDataContext()` method, from that looked up the relevant class in the FSharp github repository, and tried to call a method defined in it... but it wasn't there.

Given that the type provider is going to be relatively dynamic and connection dependent, I suspect something clever is going on under the hood. I'm going to search for `Topics` and `get_Topics` in the **WorldBankProvider.fs** file, and see what falls out.


Sure enough, nestled deep inside the `WorldBankProvider` typeprovider object there is a `ProvidedTypeDefinition` call that contains the property names I can see (see below). This makes a `WorldBankDataService` object, which handles the `WorldBankData` class internally.

```fsharp
let createTypesForSources(sources, worldBankTypeName, asynchronous) = 
    ...
    let worldBankDataServiceType =
        let t = ProvidedTypeDefinition("WorldBankDataService", Some typeof<WorldBankData>, hideObjectMethods = true, nonNullable = true)
        t.AddMembersDelayed (fun () -> 
            [ yield ProvidedProperty("Countries", countriesType,  getterCode = (fun (Singleton arg) -> <@@ ((%%arg : WorldBankData) :> IWorldBankData).GetCountries() @@>))
              yield ProvidedProperty("Regions", regionsType,  getterCode = (fun (Singleton arg) -> <@@ ((%%arg : WorldBankData) :> IWorldBankData).GetRegions() @@>))
              yield ProvidedProperty("Topics", topicsType,  getterCode = (fun (Singleton arg) -> <@@ ((%%arg : WorldBankData) :> IWorldBankData).GetTopics() @@>)) ])
        serviceTypesType.AddMember t
        t

```

All of the classes and properties that I want to access are defined in this file. They seem to be provided dynamically in some way, perhaps that's part of the "magic of TypeProviders" that I hear about, but this is something I'll have to investigate further in the future.

For now, I can infer a couple of things:
1. The WorldBankData class generates data on the fly, lazy loading them as they are requested by the caller
2. It effectively exposes 3 properties:
    * Countries
    * Regions
    * Topics

## Digging Around
Now we can have a root around in something. From the two files I can see that `Topics` exposes a `TopicCollection` to the caller, which is basically a sequence of `Topic` objects:

```fsharp

type TopicCollection<'T when 'T :> Topic> internal (connection: ServiceConnection) = 
    let items = seq { for topic in connection.Topics -> Topic(connection, topic.Id) :?> 'T } 
    interface seq<'T> with member x.GetEnumerator() = items.GetEnumerator()
    interface IEnumerable with member x.GetEnumerator() = (items :> IEnumerable).GetEnumerator()
    interface ITopicCollection with member x.GetTopic(topicCode) = Topic(connection, topicCode)
```

Further, each Topic object should contain `Code`, `Name` and `Description` properties, as well as a collection of `IndicatorDescription` objects, more on those later.

```fsharp
type Topic internal (connection:ServiceConnection, topicCode:string) = 
    let indicatorsDescriptions = new IndicatorsDescriptions(connection, topicCode)
    /// Get the WorldBank code of the topic
    member x.Code = topicCode
    /// Get the name of the topic 
    member x.Name = connection.TopicsIndexed.[topicCode].Name
    /// Get the description of the topic 
    member x.Description = connection.TopicsIndexed.[topicCode].Description
    interface ITopic with member x.GetIndicators() = indicatorsDescriptions

```

For now, I'm going to examine the contents of the object with the `Util.Table` method, which presents very pretty tables like this:


```fsharp
let topics = wb.Topics
topics
    |> Seq.take(10)
    |> Util.Table
```




<table><thead><tr><th>Code</th><th>Name</th><th>Description</th></tr></thead><tbody><tr><td>1</td><td>Agriculture &amp; Rural Development</td><td>For the 70 percent of the world&#39;s poor who live in rural areas, agriculture is the main source of income and employment. But depletion and degradation of land and water pose serious challenges to producing enough food and other agricultural products to sustain livelihoods here and meet the needs of urban populations. Data presented here include measures of agricultural inputs, outputs, and productivity compiled by the UN&#39;s Food and Agriculture Organization.</td></tr><tr><td>2</td><td>Aid Effectiveness</td><td>Aid effectiveness is the impact that aid has in reducing poverty and inequality, increasing growth, building capacity, and accelerating achievement of the Millennium Development Goals set by the international community. Indicators here cover aid received as well as progress in reducing poverty and improving education, health, and other measures of human welfare.</td></tr><tr><td>3</td><td>Economy &amp; Growth</td><td>Economic growth is central to economic development. When national income grows, real people benefit. While there is no known formula for stimulating economic growth, data can help policy-makers better understand their countries&#39; economic situations and guide any work toward improvement. Data here covers measures of economic growth, such as gross domestic product (GDP) and gross national income (GNI). It also includes indicators representing factors known to be relevant to economic growth, such as capital stock, employment, investment, savings, consumption, government spending, imports, and exports.</td></tr><tr><td>4</td><td>Education</td><td>Education is one of the most powerful instruments for reducing poverty and inequality and lays a foundation for sustained economic growth. The World Bank compiles data on education inputs, participation, efficiency, and outcomes. Data on education are compiled by the United Nations Educational, Scientific, and Cultural Organization (UNESCO) Institute for Statistics from official responses to surveys and from reports provided by education authorities in each country.</td></tr><tr><td>5</td><td>Energy &amp; Mining</td><td>The world economy needs ever-increasing amounts of energy to sustain economic growth, raise living standards, and reduce poverty. But today&#39;s trends in energy use are not sustainable. As the world&#39;s population grows and economies become more industrialized, nonrenewable energy sources will become scarcer and more costly. Data here on energy production, use, dependency, and efficiency are compiled by the World Bank from the International Energy Agency and the Carbon Dioxide Information Analysis Center.</td></tr><tr><td>6</td><td>Environment</td><td>Natural and man-made environmental resources – fresh water, clean air, forests, grasslands, marine resources, and agro-ecosystems – provide sustenance and a foundation for social and economic development.  The need to safeguard these resources crosses all borders.  Today, the World Bank is one of the key promoters and financiers of environmental upgrading in the developing world. Data here cover forests, biodiversity, emissions, and pollution. Other indicators relevant to the environment are found under data pages for Agriculture &amp; Rural Development, Energy &amp; Mining, Infrastructure, and Urban Development.</td></tr><tr><td>7</td><td>Financial Sector</td><td>An economy&#39;s financial markets are critical to its overall development. Banking systems and stock markets enhance growth, the main factor in poverty reduction. Strong financial systems provide reliable and accessible information that lowers transaction costs, which in turn bolsters resource allocation and economic growth. Indicators here include the size and liquidity of stock markets; the accessibility, stability, and efficiency of financial systems; and international migration and workers\ remittances, which affect growth and social welfare in both sending and receiving countries.</td></tr><tr><td>8</td><td>Health</td><td>Improving health is central to the Millennium Development Goals, and the public sector is the main provider of health care in developing countries. To reduce inequities, many countries have emphasized primary health care, including immunization, sanitation, access to safe drinking water, and safe motherhood initiatives.  Data here cover health systems, disease prevention, reproductive health, nutrition, and population dynamics. Data are from the United Nations Population Division, World Health Organization, United Nations Children&#39;s Fund, the Joint United Nations Programme on HIV/AIDS, and various other sources.</td></tr><tr><td>9</td><td>Infrastructure</td><td>Infrastructure helps determine the success of manufacturing and agricultural activities. Investments in water, sanitation, energy, housing, and transport also improve lives and help reduce poverty. And new information and communication technologies promote growth, improve delivery of health and other services, expand the reach of education, and support social and cultural advances. Data here are compiled from such sources as the International Road Federation, Containerisation International, the International Civil Aviation Organization, the International Energy Association, and the International Telecommunications Union.</td></tr><tr><td>10</td><td>Social Protection &amp; Labor</td><td>The supply of labor available in an economy includes people who are employed, those who are unemployed but seeking work, and first-time job-seekers. Not everyone who works is included: unpaid workers, family workers, and students are often omitted, while some countries do not count members of the armed forces. Data on labor and employment are compiled by the International Labour Organization (ILO) from labor force surveys, censuses, establishment censuses and surveys, and administrative records such as employment exchange registers and unemployment insurance schemes.</td></tr><tbody></tbody></table>



How neat is that? I've gone from knowing next to nothing about this library to being able to pull out a list of information (all of the 'topics' of data available within the World Bank dataset), and I know exactly where to dig next; pick a topic, see what it holds, repeat...

As I've already said, I've got a thing about farming, so "Agriculture & Rural Development" it is.

Now, I'd be lying if I said I hadn't already seen an example of getting a specific country from the `Countries` collection. I know that the API uses named properties with the full spaces-and-everything name of the country in it, so **I can take a punt that the same will be true with everything else**. 

I would guess that the following will return the data I'm after:

``` topics.``Agriculture & Rural Development`` ```

However, before I try that I'm going to look into how this work, as if I didn't have that example to hand. Feel free to skip ahead until the **Ploughing Onwards** section.


### [Extra] Deeper digging for the very keen

From the TopicCollection class there is a method that looks promising; `GetTopic(topicCode)`. However, I know something strange is happening to the types in this library, so I'm going to take a look at `WorldBankDataServide` object in the **WorldBankProvider.fs** file again, specifically at the `topicsType` property.

`GetIndicator(indicatorCode)`

Here it is:
```fsharp
let topicsType =
            let topicCollectionType = ProvidedTypeBuilder.MakeGenericType(typedefof<TopicCollection<_>>, [ topicType ])
            let t = ProvidedTypeDefinition("Topics", Some topicCollectionType, hideObjectMethods = true, nonNullable = true)
            t.AddMembersDelayed (fun () -> 
                [ for topic in connection.Topics do
                    let topicIdVal = topic.Id
                    let prop = 
                        ProvidedProperty
                          ( topic.Name, topicType, 
                            getterCode = (fun (Singleton arg) -> <@@ ((%%arg : TopicCollection<Topic>) :> ITopicCollection).GetTopic(topicIdVal) @@>))
                    if not (String.IsNullOrEmpty topic.Description) then prop.AddXmlDoc(topic.Description)
                    yield prop ])
            serviceTypesType.AddMember t
            t
```
As you can see there is a call to `AddMembersDelayed` to the dynamic `ProvidedTypeDefinition` that we're clearly encountering here in the real world. 'Delayed' presumably means that the data is loaded on request, and not before (sensible in an online API), but more importantly we see that the method is yielding a collection of dynamic `ProvidedProperty` objects, each named after `Topic.Name` (i.e. "Agriculture & RuralDevelopment"), and with something called a getterCode that takes the `Topic.Id` and calls it using the `TopicCollection.GetTopic()` method.

And, the fact that all of these properties are lazy loaded explains why my attempt at `GetProperties()` didn't work.

Great! So presumably, I look for properties with the name of the Topic...

... but what about the spaces and ampersand? Perhaps I should start with something simple:


```fsharp
topics.Trade.GetType()
```




    FSharp.Data.Runtime.WorldBank.Topic



Excellent! We're on the right track! Just for the hell of it I'll try the full name for Agriculture:


```fsharp
topics.Agriculture & RuralDevelopment.GetType()
```

    In F# code, use 'e1 && e2' instead of 'e1 & e2'
    The field, constructor or member 'Agriculture' is not defined. Maybe you want one of the following:
       Agriculture & Rural Development
    The value, namespace, type or module 'RuralDevelopment' is not defined.

As expected, but helpfully (or not), it tells me that the property does exist.

After some thinking and searching, I learn something new about F# (hooray). Double back-quotes are use to make phrases into valid identifiers:

https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/symbol-and-operator-reference/

```
    ``...``   Delimits an identifier that would otherwise not be a legal identifier, such as a language keyword.
``` 
        
Now this admittedly wasn't an easy leap, but once you now it, you know it. Similar tools are used in other languages, in R you can definitely name columns of dataframes in such a way, and clearly that's the case in F# as well.

So there we are, all that remains is to try it out!


### Ploughing Onwards


```fsharp
let agricultureTopic = topics.``Agriculture & Rural Development``

agricultureTopic.GetType()
```




    FSharp.Data.Runtime.WorldBank.Topic




```fsharp
agricultureTopic.Description
```




    "For the 70 percent of the world's poor who live in rural areas, agriculture is the main source of income and employment. But depletion and degradation of land and water pose serious challenges to producing enough food and other agricultural products to sustain livelihoods here and meet the needs of urban populations. Data presented here include measures of agricultural inputs, outputs, and productivity compiled by the UN's Food and Agriculture Organization."



This is good to see!

Now I'm going to apply these same tricks to the `Topic.Indicators` property and see what's available:


```fsharp
let agricultureIndicators = agricultureTopic.Indicators

agricultureIndicators 
    |> Seq.take(10)
    |> Util.Table
```




<table><thead><tr><th>Code</th><th>IndicatorCode</th><th>Name</th><th>Source</th><th>Description</th></tr></thead><tbody><tr><td>1</td><td>AG.CON.FERT.ZS</td><td>Fertilizer consumption (kilograms per hectare of arable land)</td><td>World Development Indicators</td><td>Fertilizer consumption measures the quantity of plant nutrients used per unit of arable land. Fertilizer products cover nitrogenous, potash, and phosphate fertilizers (including ground rock phosphate). Traditional nutrients--animal and plant manures--are not included. For the purpose of data dissemination, FAO has adopted the concept of a calendar year (January to December). Some countries compile fertilizer data on a calendar year basis, while others are on a split-year basis. Arable land includes land defined by the FAO as land under temporary crops (double-cropped areas are counted once), temporary meadows for mowing or for pasture, land under market or kitchen gardens, and land temporarily fallow. Land abandoned as a result of shifting cultivation is excluded.</td></tr><tr><td>1</td><td>AG.CON.FERT.PT.ZS</td><td>Fertilizer consumption (% of fertilizer production)</td><td>World Development Indicators</td><td>Fertilizer consumption measures the quantity of plant nutrients used per unit of arable land. Fertilizer products cover nitrogenous, potash, and phosphate fertilizers (including ground rock phosphate). Traditional nutrients--animal and plant manures--are not included. For the purpose of data dissemination, FAO has adopted the concept of a calendar year (January to December). Some countries compile fertilizer data on a calendar year basis, while others are on a split-year basis.</td></tr><tr><td>1</td><td>AG.AGR.TRAC.NO</td><td>Agricultural machinery, tractors</td><td>World Development Indicators</td><td>Agricultural machinery refers to the number of wheel and crawler tractors (excluding garden tractors) in use in agriculture at the end of the calendar year specified or during the first quarter of the following year.</td></tr><tr><td>1</td><td>AG.YLD.CREL.KG</td><td>Cereal yield (kg per hectare)</td><td>World Development Indicators</td><td>Cereal yield, measured as kilograms per hectare of harvested land, includes wheat, rice, maize, barley, oats, rye, millet, sorghum, buckwheat, and mixed grains. Production data on cereals relate to crops harvested for dry grain only. Cereal crops harvested for hay or harvested green for food, feed, or silage and those used for grazing are excluded. The FAO allocates production data to the calendar year in which the bulk of the harvest took place. Most of a crop harvested near the end of a year will be used in the following year.</td></tr><tr><td>1</td><td>AG.SRF.TOTL.K2</td><td>Surface area (sq. km)</td><td>World Development Indicators</td><td>Surface area is a countrys total area, including areas under inland bodies of water and some coastal waterways.</td></tr><tr><td>1</td><td>AG.PRD.LVSK.XD</td><td>Livestock production index (2004-2006 = 100)</td><td>World Development Indicators</td><td>Livestock production index includes meat and milk from all sources, dairy products such as cheese, and eggs, honey, raw silk, wool, and hides and skins.</td></tr><tr><td>1</td><td>AG.PRD.FOOD.XD</td><td>Food production index (2004-2006 = 100)</td><td>World Development Indicators</td><td>Food production index covers food crops that are considered edible and that contain nutrients. Coffee and tea are excluded because, although edible, they have no nutritive value.</td></tr><tr><td>1</td><td>AG.PRD.CROP.XD</td><td>Crop production index (2004-2006 = 100)</td><td>World Development Indicators</td><td>Crop production index shows agricultural production for each year relative to the base period 2004-2006. It includes all crops except fodder crops. Regional and income group aggregates for the FAOs production indexes are calculated from the underlying values in international dollars, normalized to the base period 2004-2006.</td></tr><tr><td>1</td><td>AG.PRD.CREL.MT</td><td>Cereal production (metric tons)</td><td>World Development Indicators</td><td>Production data on cereals relate to crops harvested for dry grain only. Cereal crops harvested for hay or harvested green for food, feed, or silage and those used for grazing are excluded.</td></tr><tr><td>1</td><td>AG.LND.TRAC.ZS</td><td>Agricultural machinery, tractors per 100 sq. km of arable land</td><td>World Development Indicators</td><td>Agricultural machinery refers to the number of wheel and crawler tractors (excluding garden tractors) in use in agriculture at the end of the calendar year specified or during the first quarter of the following year. Arable land includes land defined by the FAO as land under temporary crops (double-cropped areas are counted once), temporary meadows for mowing or for pasture, land under market or kitchen gardens, and land temporarily fallow. Land abandoned as a result of shifting cultivation is excluded.</td></tr><tbody></tbody></table>




```fsharp
let cerealYield = agricultureIndicators.``Cereal yield (kg per hectare)``

cerealYield.GetType()
```




    FSharp.Data.Runtime.WorldBank.IndicatorDescription



Great, so if I take a look at the Indicator type, I see there's an `Item` property, which is familiar from dictionaries in .NET, that takes a year as an id and looks up in an internal dictionary. Note as well that the `Indicator` class inherits from `seq<int * float>`, which suggests we should be able to bring the mighty `Seq` tools to bear on it later... Good job! We're nearly there!

```fsharp
/// Indicator data
type Indicator internal (connection:ServiceConnection, countryOrRegionCode:string, indicatorCode:string) = 
    let data = connection.GetData(countryOrRegionCode, indicatorCode) |> Seq.cache
    let dataDict = lazy (dict data)
    ...
    member x.Item
        with get year = 
            match dataDict.Force().TryGetValue year with
            | true, value -> value
            | _ -> Double.NaN
    ...
    interface seq<int * float> with member x.GetEnumerator() = data.GetEnumerator()
    interface IEnumerable with member x.GetEnumerator() = (data.GetEnumerator() :> _)
```


```fsharp
let cerealYield = agricultureIndicators.``Cereal yield (kg per hectare)``.["2016"] 
```

    The field, constructor or member 'Item' is not defined.

Oh no!

We got as far as trying to pull out the data for a specific year, but there wasn't any data in it?

This is because I didn't read the name of the class properly, my `cerealYield` object isn't an `Indicator`, it's an `IndicatorDescription` - metadata only. In order to access the actual data we need to get the `Indicator` collection of a country. 



```fsharp
let countries = wb.Countries

countries
    |> Seq.take(10)
    |> Util.Table
```




<table><thead><tr><th>Code</th><th>Name</th><th>CapitalCity</th><th>Region</th></tr></thead><tbody><tr><td>ABW</td><td>Aruba</td><td>Oranjestad</td><td>Latin America &amp; Caribbean </td></tr><tr><td>AFG</td><td>Afghanistan</td><td>Kabul</td><td>South Asia</td></tr><tr><td>AGO</td><td>Angola</td><td>Luanda</td><td>Sub-Saharan Africa </td></tr><tr><td>ALB</td><td>Albania</td><td>Tirane</td><td>Europe &amp; Central Asia</td></tr><tr><td>AND</td><td>Andorra</td><td>Andorra la Vella</td><td>Europe &amp; Central Asia</td></tr><tr><td>ARE</td><td>United Arab Emirates</td><td>Abu Dhabi</td><td>Middle East &amp; North Africa</td></tr><tr><td>ARG</td><td>Argentina</td><td>Buenos Aires</td><td>Latin America &amp; Caribbean </td></tr><tr><td>ARM</td><td>Armenia</td><td>Yerevan</td><td>Europe &amp; Central Asia</td></tr><tr><td>ASM</td><td>American Samoa</td><td>Pago Pago</td><td>East Asia &amp; Pacific</td></tr><tr><td>ATG</td><td>Antigua and Barbuda</td><td>Saint John&#39;s</td><td>Latin America &amp; Caribbean </td></tr><tbody></tbody></table>



Well, there's a lot (see *"vastly, mid-bogglingly big"* from the intro), how about the UK?


```fsharp
let ukCerealYields = wb.Countries.``United Kingdom``.Indicators.``Cereal yield (kg per hectare)``

ukCerealYields.GetType()
```




    FSharp.Data.Runtime.WorldBank.Indicator




```fsharp
ukCerealYields
    |> Seq.take(10)
    |> Util.Table
```




<table><thead><tr><th>Item1</th><th>Item2</th></tr></thead><tbody><tr><td>1961</td><td>3176.5</td></tr><tr><td>1962</td><td>3683.5</td></tr><tr><td>1963</td><td>3484.3</td></tr><tr><td>1964</td><td>3734.7</td></tr><tr><td>1965</td><td>3750.1</td></tr><tr><td>1966</td><td>3544.7</td></tr><tr><td>1967</td><td>3828.7</td></tr><tr><td>1968</td><td>3446.5</td></tr><tr><td>1969</td><td>3670.8</td></tr><tr><td>1970</td><td>3568.9</td></tr><tbody></tbody></table>



## Reaping the rewards

Grand. Now I have a set of data to access. I know that it behaves like a sequence, but I can get specific points like a dictionary, it loads data from the World Bank data set as I request it, ... I should be good to go!

There are lots of things to do next, I think I'll save them for another post. I hope you've found this helpful, I know I've learnt a lot, but above all I wanted to get across the tools I use to solve problems when there's little else to go on. The free access to all of this source code on github is an amazing, beautiful, thing, and it shouldn't be taken for granted. The same goes for tools like NuGet and Paket; I've been able to take a random file of text from a globally distributed storage architecture, plug it into a system on my own PC, download data from somewhere else in the world and print it out for you here... if you believe Arthur C Clarke, **this is *literally* magic**. It's awesome, and I will never stop loving it. I hope you get some of that feeling too.

Anyway, thanks for reading, feel free to download the notebook and run it yourself, if it helps. Otherwise, good luck, happy number crunching, and keep your eye out for my next post!
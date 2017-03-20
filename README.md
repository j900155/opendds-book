# **CHAPTER 1**

### **Introduction**


OpenDDS是建立於 OMG Data Distribution Service (DDS) 的 Real-Time Systems V1.4(OMG 文件格式/2015-04-10) 和 Real-time Publish-Subscribe Wire Protocol DDS Interoperability Wire Protocol Specification(DDSI-RTPS) v2.2 (OMG Document formal/2014-09-01)。OpenDDS 是由 Object Computing, Inc. (OCI) 贊助。 Opendds 網址為[http://www.opendds.org/](http://opendds.org/)。開發人員指南基於 3.10 釋出版本的 Opendds。

DDS 的定義為在分散式的應用程式有效的傳輸分散式的應用程式資料。這個服務不特定為 CORBA 。提供 Platform Independent Model (PIM) 以及將 Platform Specific Model (PSM) 映射成 CORBA IDL。

有關DDS的其他詳細信息，開發人員應參考DDS規範（OMG文件格式/ 2015-04-10），因為它包含對所有服務的功能的深入涵蓋。

OpenDDS 是 C++ 所寫的開原軟體，OCI 提供 OMG's DDS 規範開發和商業支持。能夠從[http://www.opendds.org/downloads.html](http://www.opendds.org/downloads.html)下載和 OCI TAO version 2.0a and 2.2a 級別的補丁以及最新的文件。

注意:OpenDDS目前實現OMG DDS 1.4版規範。 有關詳細訊息，請參閱[http://www.opendds.org/](http://www.opendds.org/)中的規格訊息。

# 1.1. DCPS概述

在本節中，我們將介紹DCPS層的主要概念和實體，並討論它們如何相互影響和一起工作。

## 1.1.1基本概念

圖1-1顯示了DDS DCPS層的概述。 子節點的概念定義再下面那張圖中所示。

### ![](/1.1.jpg)

### 1.1.1.1 Domain

Domain 是 DCPS 中的基本分區單元。每個實體屬於一個domain，並且只能與該 domain 中的其他實體相互影響。

程式碼可以自由的與多個 domain 作用，但必需藉由分離不同 domain 的實體來做到。

### 1.1.1.2 DomainParticipant

domain participant是應用程序與特定的 domain 互相作用的入口點。domain participant 是個工廠給許多物件參與讀取寫入的地方。

### 1.1.1.3 Topic

在應用程序中 Topic 是publishing 和 subscribing 溝通的基本手段。每個 topiic 在 domain 中不能重複，以及發布它的特定數據類型。每個主題數據類型可以指定組零個或多個字段以及他的key(make up its key)。在發送資料時,發送程序總是會指定一個 topic 。Subscribers 通過 topic 請求數據。在DCPS術語中，你在 topic 推送個人不同的範例資料。每個實例都與唯一的 key 有關聯。推送過程中多個相同實例範本資料使用相同的key。

### 1.1.1.4 DataWriter

發布應用程序代碼使用數據寫入程序將值傳遞到DDS。 每個數據寫入器都綁定到一個特定的主題。 應用程序使用數據寫入器的特定類型的接口來發布有關該主題的樣本。 數據寫入器負責編組數據並將其傳遞給發佈器進行傳輸。

### =====1.1.1.5發布者

發布商負責獲取發布的數據並將其傳播到域中的所有相關訂閱者。 所使用的確切機制由服務實現決定。

### 1.1.1.6訂閱者

訂閱者從發布者接收數據並將其傳遞到與其相連的任何相關數據讀取器。

### 1.1.1.7 DataReader

數據讀取器從訂戶獲取數據，將其劃分為適合該主題的類型，並將樣本傳送到應用程序。 每個數據讀取器都綁定到特定主題。 應用程序使用數據讀取器的類型特定接口來接收樣本。

## 1.1.2 內置主題

DDS規範定義了內置於DDS實現中的許多主題。 訂閱這些內置主題使應用開發人員能夠訪問所使用的域的狀態，包括哪些主題被註冊，哪些數據讀取器和數據寫入器被連接和斷開，以及各種實體的QoS設置。 在訂閱時，應用程序接收指示域內實體的改變的樣本。

下表顯示了在DDS規範中定義的內置主題：

| 主題名稱 | 描述 |
| :--- | :--- |
| DCPSParticipant | 每個實例表示一個域參與者。 |
| DCPSTopic Each | 每個實例表示正常（非內置）主題。 |
| DCPSPublication | 每個實例表示數據寫入程序。 |
| DCPSSubscription | 每個實例表示一個數據讀取器。 |

## 1.1.3 服務質量政策

DDS規範定義了許多服務質量（QoS）策略，應用程序使用這些策略來指定其對服務的QoS要求。 參與者指定他們從服務中需要什麼行為，服務決定如何實現這些行為。 這些策略可以應用於各種DCPS實體（主題，數據寫入器，數據讀取器，發布者，訂閱者，域參與者），儘管不是所有策略都對所有類型的實體有效。

訂閱者和發布者使用請求 - 提供（RxO）模型來匹配。

訂閱者請求一組最低限度需要的策略。 發布者向潛在訂戶提供一組QoS策略。 然後，DDS實現嘗試將所請求的策略與所提供的策略進行匹配; 如果這些策略是兼容的，則形成關聯。

OpenDDS當前實現的QoS策略將在第3章中詳細討論。

## 1.1.4 Listeners

DCPS層為每個實體定義回調接口，其允許應用進程“監聽”關於該實體的某些狀態改變或事件。 例如，當有可用於讀取的數據值時，通知數據讀取器偵聽器。

### 1.1.5條件

條件和等待集允許在偵聽DDS中感興趣的事件時使用偵聽器的替代方案。 一般模式是應用程序創建一個特定類型的Condition對象，例如StatusCondition，並將其附加到WaitSet。

•應用程序等待WaitSet，直到一個或多個條件成為真。

•應用程序調用對相應實體對象的操作以提取必要的信息。

•DataReader接口還具有讀取ReadCondition參數的操作。

•QueryCondition對像作為Content-Subscription配置文件實現的一部分提供。QueryCondition接口擴展了ReadCondition接口。

## 1.2 OpenDDS實現

### 1.2.1合規性

OpenDDS符合OMG DDS和OMG DDSI-RTPS規範。 遵守的細節在這裡。

### 1.2.1.1 DDS合規性

DDS規範的第2節定義了DDS實現的五個合規點：

1）最低配置文件

2）內容訂閱簡檔

3）持久性概況

4）所有權簡介

5）對像模型配置文件

OpenDDS符合整個DDS規範（包括所有可選配置文件）。 這包括實施所有服務質量政策，並註意以下事項：

•僅當使用TCP或IP多播傳輸（配置為可靠）或使用RTPS\_UDP傳輸時，才支持RELIABILITY.kind = RELIABLE。

•TRANSPORT\_PRIORITY未實現為可更改。

### 1.2.1.2 DDSI-RTPS合規性

OpenDDS實現符合OMG DDSI-RTPS規範的要求。

OpenDDS RTPS實現註釋OMG DDSI-RTPS規範（正式/ 2014-09-01）提供的語句

實現，但不是合規性要求。在使用OpenDDS RTPS功能進行傳輸和/或發現時，應考慮以下各項。每個項目提供了DDSI-RTPS規範的部分編號，以供進一步參考。

#### 未在OpenDDS中實現的項目：

1）寫入器端內容過濾（8.7.3）OpenDDS可能仍然丟棄任何相關讀者不需要的樣本（由於內容過濾） - 這是在傳輸層之上完成的

2）用於演示QoS的相干集（8.7.5）

3）定向寫入（8.7.6）

4）屬性列表（8.7.7）

5）DURABLE數據的原始寫入者信息（8.7.8） - 這將僅用於臨時和持久持久性，這是RTPS規範不支持的（8.7.2.2.1）

6）不生成密鑰散列（8.7.9），但它們是可選的

7）nackSuppressionDuration（表8.47）和heartbeatSuppressionDuration

（表8.62）。

注意:上面的項目3和4在DDSI-RTPS規範中描述。 然而，它們在DDS規範中沒有相應的概念。

## 1.2.2擴展到DDS規範

DDS IDL模塊（C ++命名空間，Java包）中的數據類型，接口和常量直接對應於DDS規範，極少有例外：

•DDS :: SampleInfo包含一個以“opendds\_reserved”開頭的附加字段，

•特定於類型的DataReaders（包括內置主題的DataReaders）具有其他操作read\_instance\_w\_condition（）和take\_instance\_w\_condition（）。

額外的擴展行為由OpenDDS模塊/命名空間/包中的各種類和接口提供。 這些功能包括Recorder和Replayer（見第12章）以及以下功能：

•OpenDDS :: DCPS :: TypeSupport添加在DDS規範中未找到的unregister\_type（）操作。

•OpenDDS :: DCPS :: ALL\_STATUS\_MASK，NO\_STATUS\_MASK和DEFAULT\_STATUS\_MASK是DDS :: Entity，DDS :: StatusCondition和各種create \_ \*（）操作使用的DDS :: StatusMask類型的有用常量。

## 1.2.3 OpenDDS架構

本節簡要概述了OpenDDS實現，其特性及其一些組件。 $ DDS\_ROOT環境變量應指向OpenDDS發行版的基本目錄。 OpenDDS的源代碼可以在$ DDS\_ROOT / dds /目錄下找到。 DDS測試可以在$ DDS\_ROOT / tests /下找到。

### 1.2.3.1 設計哲學

OpenDDS實現和API基於對OMG IDL PSM的相當嚴格的解釋。在幾乎所有情況下，OMG的CORBA IDL的C ++語言映射用於定義DDS規範中的IDL如何映射到OpenDDS向客戶端公開的C ++ API。

與OMG IDL PSM的主要偏離在於本地接口用於實體和各種其他接口。這些在DDS規範中定義為非約束（非本地）接口。將它們定義為本地接口可以提高性能，減少內存使用，簡化客戶端與這些接口的交互，並使客戶端更容易構建自己的實現。

### 1.2.3.2 可擴展傳輸框架（ETF）

OpenDDS使用DDS規範定義的IDL接口來初始化和控制服務使用。數據傳輸通過OpenDDS特定的傳輸框架來實現，該傳輸框架允許服務與各種傳輸協議一起使用。這被稱為可插拔傳輸，使OpenDDS的可擴展性成為其架構的重要組成部分。 OpenDDS目前支持TCP / IP，UDP / IP，IP多播，共享存儲和RTPS\_UDP傳輸協議，如圖1-2所示。傳輸通常通過配置文件指定，並附加到發布者和訂閱者進程中的各種實體。有關配置ETF組件的詳細信息，請參見第7.4.4節。

![](/1.2.jpg)

ETF使應用程序開發人員能夠實現自己的定制傳輸。

實現自定義傳輸涉及專門化在傳輸框架中定義的多個類。 udp傳輸提供了開發人員在創建自己的實現時可以使用的良好基礎。 有關詳細信息，請參閱$ DDS\_ROOT / dds / DCPS / transport / udp /目錄。

### 1.2.3.3 DDS發現

DDS應用程序必須通過某個中央代理或通過某種分佈式方案彼此發現。 OpenDDS的一個重要特性是，DDS應用程序可以配置為使用DCPSInfoRepo或RTPS發現執行發現，但利用不同的傳輸類型在數據寫入器和數據讀取器之間進行數據傳輸。 OMG DDS規範（正式/ 2015-04-10）將發現的細節留給實現。在DDS實現之間的互操作性的情況下，OMG DDSI-RTPS（正式/ 2014-09-01）規範提供了對等發現的對等方式的要求。

OpenDDS提供兩個發現選項。

1）信息庫：一種集中的存儲庫風格，作為一個單獨的過程運行，允許發布者和訂閱者集中發現另一個

2）RTPS發現：使用RTPS協議來通告可用性和位置信息的對等風格的發現。

與其他DDS實現的互操作性必須利用對等方法，但在僅支持OpenDDS的部署中很有用。

### 使用DCPSInfoRepo進行集中式發現

OpenDDS實現一個稱為DCPS信息庫的獨立服務

（DCPSInfoRepo）實現集中式發現方法。 它被實現為CORBA服務器。 當客戶端請求主題的預訂時，DCPS信息庫定位主題並且通知任何現有的發布者新訂戶的位置。

每當在非RTPS配置中使用OpenDDS時，DCPSInfoRepo進程需要運行。 RTPS配置不使用DCPSInfoRepo。 DCPSInfoRepo不參與數據傳播，其作用範圍受到OpenDDS應用程序發現彼此的限制。

![](/1.3.jpg)

應用程序開發人員可以自由運行多個信息庫，每個管理其自己的非重疊的DCPS域集合。

還可以操作具有多於單個存儲庫的域，從而形成分佈式虛擬存儲庫。這稱為存儲庫聯合。為了使各個存儲庫參與聯合，每個庫必須在啟動時指定其自己的聯合標識符值（一個32位數字值）。有關存儲庫聯合的更多信息，請參見9.2。

### 使用RTPS的對等發現

需要對等發現模式的DDS應用程序可以由OpenDDS功能提供。這種發現方式僅通過使用當前版本的RTPS協議來實現。這種簡單的發現形式是通過在應用程序進程中運行的DDS應用程序數據讀取器和數據寫入程序的簡單配置來實現的，如錯誤：未找到參考源。由於每個參與的進程為其數據讀取器和寫入器激活OpenDDS中的DDSI-RTPS發現機制，因此使用默認或配置的網絡端口來創建網絡端點，使得DDS參與者可以開始廣告其數據讀取器和數據寫入器的可用性。在一段時間之後，基於標準尋找彼此的那些將彼此找到並且基於如在可擴展傳輸框架（ETF）中討論的配置的可插入傳輸建立連接。這種靈活配置方法的更詳細描述在第7.4.1.1節和第7.4.5.5節中討論。

以下是開發人員在開發和部署使用RTPS發現的應用程序時需要考慮的其他實現限制：1）由於UDP端口分配給域ID的方式，域ID應在0到231（含）之間。在每個OpenDDS流程中，每個域中最多支持120個域參與者。

2）主題名稱和類型標識符限制為256個字符。

3）由於分配GUID的方式（如果嘗試，將發出警告），OpenDDS的本地多播傳輸不與RTPS發現一起工作。

關於RTPS發現如何發生的更多細節，可以在實時發布 - 訂閱有線協議DDS互操作性有線協議規範（DDSI-RTPS）v2.2（OMG文件格式/ 2014 -09-01）。

### 1.2.3.4 Threading

OpenDDS創建自己的ORB（當需要一個ORB時）以及運行該ORB的單獨的線程。 它還使用自己的線程來處理傳入和傳出傳輸I / O。 創建單獨的線程以在意外連接關閉時清除資源。 您的應用程序可能會通過DCPS的偵聽器機制從這些線程中調用。

當通過DDS發布樣本時，OpenDDS通常嘗試使用調用線程將樣本發送到任何已連接的訂閱者。 如果發送調用塊，則樣本可以排隊等待在單獨的服務線程上發送。 此行為取決於第3章中描述的QoS策略。

訂戶中的所有傳入數據由服務線程讀取並排隊等待應用程序讀取。 從服務線程調用DataReader偵聽器。

### 1.2.3.5配置

OpenDDS包括一個基於文件的配置框架，用於配置全局項，如調試級別，內存分配和發現，以及發布商和訂閱者的傳輸實現詳細信息。配置也可以直接在代碼中實現，但是，建議將配置外部化，以便於維護和減少運行時錯誤。第7章介紹了完整的配置選項集。

## 1.3安裝

有關如何構建OpenDDS的步驟可以在$ DDS\_ROOT / INSTALL中找到。為了避免編譯您不會使用的OpenDDS代碼，有一些功能比可以從構建中排除。下面討論這些特徵。需要小尺寸配置或與安全性平台兼容性的用戶應考慮使用本指南第13章中所述的OpenDDS安全配置文件。

### 1.3.1啟用或禁用功能的構建

大多數功能都由configure腳本支持。 configure腳本創建具有正確內容的配置文件，然後運行MPC。如果使用configure腳本，請使用“--help”命令行選項運行它，並查找要啟用/禁用的功能。

如果不使用configure腳本，請繼續閱讀以下有關直接運行MPC的說明。

對於下述功能，MPC用於啟用（默認）功能或禁用功能。對於名為feature的功能部件，使用以下步驟從構建中禁用功能部件：

1）使用MPC的命令行“features”參數：

mwc.pl -type &lt;type&gt; -features feature = 0 DDS.mwc或者，將line feature = 0添加到文件$ ACE\_ROOT / bin / MakeProjectCreator / config / default.features中，並使用MPC重新生成項目文件。

2）如果您使用gnuace MPC項目類型（如果您將使用GNU make作為您的構建系統，則是這種情況），請將文件$ ACE\_ROOT / include / makeinclude / platform\_macros.GNU中添加“feature = 0”。

要顯式啟用該功能，請使用上面的feature = 1。

**注意:**您還可以使用$ DDS\_ROOT / configure腳本啟用或禁用功能。 要禁用該功能，請將--no-feature傳遞給腳本，以啟用pass -feature。 在這種情況下，在要素名稱中使用“ - ”而不是“\_”。 例如，要禁用下面討論的功能content\_subscription，請將-no-content-subscription傳遞給configure腳本。

### 1.3.2禁用內置主題的構建

支持功能名稱：built\_in\_topics通過禁用內置主題支持，可以將核心DDS庫的佔用空間減少高達30％。請參見第6章以確定是否構建沒有BIT支持。

### 1.3.3禁用合規性配置文件特性的建立

DDS規範定義了遵從性概況以提供用於指示DDS實現可能支持或可能不支持的某些特徵集合的通用術語。以下給出這些配置文件以及用於禁用對該配置文件或該配置文件的組件的支持的MPC功能的名稱。

許多配置文件選項涉及QoS設置。如果嘗試使用與禁用的配置文件不兼容的QoS值，則會發生運行時錯誤。如果配置文件涉及類，則如果嘗試使用該類並禁用配置文件，則會發生編譯時錯誤。

#### 1.3.3.1內容訂閱配置文件

功能名稱：content\_subscription

此配置文件添加第5章中討論的類ContentFilteredTopic，QueryCondition和MultiTopic。此外，可以通過使用下表中給出的功能來排除個別類。

**Table 1-2: Content-Subscription Class Features**

| Class | Feature |
| :--- | :--- |
| ContentFilteredTopic | content\_filtered\_topic |
| QueryCondition | query\_condition |
| MultiTopic | multi\_topic |

#### 1.3.3.2持續性簡介

功能名稱：persistence\_profile

此配置文件添加QoS策略DURABILITY\_SERVICE和DURABILITY QoS策略類型的設置“TRANSIENT”和“PERSISTENT”。

#### 1.3.3.3所有權簡介

功能名稱：ownership\_profile

此配置文件添加：

•所有權類型的設置“EXCLUSIVE”

•支持OWNERSHIP\_STRENGTH策略

•為HISTORY QoS策略設置深度&gt; 1。

**注意:**目前，即使已禁用ownership\_profile，仍支持HISTORY 深度&gt; 1的OpenDDS代碼。

#### 1.3.3.4對像模型概要

功能名稱：object\_model\_profile

此配置文件包括對“GROUP”的presentENTATION access\_scope設置的支持。

**注意:**目前，當object\_model\_profile被禁用時，“TOPIC”的PRESENTATION access\_scope也被排除。





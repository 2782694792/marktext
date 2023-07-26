# cfgManager

- 一个单独的服务组件，可供其它程序调用以提供它的服务；
- 主要功能：以统一的接口，统一的存取管理方式实现对配置数据的访问。

## 特点

1. 可存储配置数据源：XML、INI、系统注册表、ACCESS文件；
2. 提供由 VC、BC、VBScript、JScript 等使用的接口；
3. 支持：新建、删除配置节（Section）、配置项；
4. 配置值：读、写数值、字符串、二进制三种类型；
5. 允许批量读写、支持枚举。

## 接口指引

#### **ICfgAccess**

ICfgAccess 是访问配置数据的主接口，方法如下所示：

| 接口函数                       | 说明                                      |
| ------------------------------ | ----------------------------------------- |
| CfgCreateSource                | 创建新的配置文件                          |
| CfgOpenData                    | 打开配置数据源                            |
| CfgCloseData                   | 关闭                                      |
| CfgCreateSection               | 创建一个配置节                            |
| CfgDeleteSection               | 删除                                      |
| CfgGet(String/Int/Binary)Value | 读取配置键的值                            |
| CfgSet(String/Int/Binary)Value | 更新                                      |
| CfgCreateKey                   | 创建配置键                                |
| CfgDeleteKey                   | 删除                                      |
| CfgEnumChilds                  | 枚举所有子配置节                          |
| CfgCommit                      | 确认所有的更新，将更新写入文件            |
| CfgGetValues                   | 一次返回同一个配置节下多个配置键的值      |
| CfgSetValues                   | 更新                                      |
| CfgLock                        | 锁定,如果不能锁定，一直等待直到锁定或超时 |
| CfgUnlock                      | 解锁                                      |
| CfgGetAllofSameKey             | 得到不同配置节下同 key 的值               |
| CfgCleanup                     | 清除没用的配置                            |

#### **ICfgCollection**

ICfgCollection 是枚举某配置节下的所有子配置节和键的集合。

| Item     | 得到元素         |
| -------- | ---------------- |
| Count    | 集合中的元素数量 |
| _NewEnum | 用于VB中的枚举   |

#### **ICfgItem**

ICfgItem 是 ICfgCollection集合中的元素。

| ItemName  | 元素名字 |
| --------- | -------- |
| ItemValue | 元素值   |
| ItemType  | 元素类型 |

#### **常量**

| CFG_SUCCESS         | 操作成功                                 |
| ------------------- | ---------------------------------------- |
| CFG_FAIL            | 失败                                     |
| CFG_NODATASOURCE    | 没有数据源                               |
| CFG_INVALIDSECTION  | 无效的配置节                             |
| CFG_INVALIDKEY      | 无效的配置键                             |
| CFG_INVALIDDATATYPE | 无效的数据类型                           |
| CFG_LOCKED          | 被其它使用者锁定                         |
| CFG_INIFILE         | INI 文件类型                             |
| CFG_XMLFILE         | XML                                      |
| CFG_DEFAULTCFGDB    | 默认的由配置管理组件自已提供的配置数据源 |
| CFG_REGISTER        | 系统注册表配置数据源                     |
| CFG_ACCESSDB        | ACCESS数据库配置数据源                   |

## 使用步骤

1. 创建 ICfgAccess 的实例；
2. 调用 CfgOpenData 打开配置数据源；
3. 调用 CfgCreateSection, CfgCreateKey, CfgSetStringValue, CfgSetIntValue 更新配置；
4. 调用 CfgGetStringValue，CfgGetIntValue 返回配置信息；
5. 调用 CfgEnumChilds 枚举某配置节下的所有配置节和配置键；
6. 使用 CfgCloseData 关闭配置数据源。 

### 1. 创建配置文件

```cpp
HRESULT CfgCreateSource(BSTR p_bstrSource, short p_sSourceType，short *p_psRet); 
```

- **p_bstrSource** 要创建的配置文件的文件名及路径；

- **p_sSourceType** 配置源的类型，有以下取值：

  - CFG_INIFILE：INI 配置文件；
  - CFG_XMLFILE：XML 配置文件；
  - CFG_REGISTER: 系统注册表；

  > 返回值：返回布尔值表示成功或失败。 

### 2. 打开配置数据源	

```cpp
HRESULT CfgOpenData(
	short p_sSourceType, 
	BSTR p_bstrSource，
	short p_psCreateNew, 
	BSTR p_bstrExt,
	short* p_psRet
);
```

- **p_sSourceType**：配置文件的类型，有以下取值：
  - CFG_DEFAULTCFGDB：默认的由配置管理组件自已提供的配置数据源。使用此参数时，p_bstrSource将被忽略；
  - CFG_REGISTER：使用系统注册表，使用此参数时，p_bstrSource 必须要打开的注册表中的项的路径，如 "HKEY_LOCAL_MACHINE\SOFTWARE\ATM"；
  - CFG_INIFILE：使用 INI 文件，p_bstrSource 必须指明文件的详细路径；
  - CFG_XMLFILE：使用 XML 文件，p_bstrSource 必须指明文件的详细路径；
  - CFG_ACCESSDB：使用 ACCESS 数据库，p_bstrFilePath 必须指明访问数据库的详细信息；

- **p_bstrSource**: 访问配置文件的完整信息;

- **p_psCreateNew**: 如果要打开的不存在，是否自动创建新的，1表示是，0表示否;

- **p_bstrExt**: 扩展的参数，暂时保留；

- > 返回值：CFG_SUCCESS：成功。 CFG_FAIL：其它错误。 

#### 2.1 关闭配置数据源

```cpp
HRESULT CfgCloseData(short* p_psRet);
```

- **返回值**：

  - CFG_SUCCESS：成功；
  - CFG_NODATASOURCE：没有数据源打开；
  - CFG_FAIL：其它错误。  

  > 注：配置数据源在关闭后，所有其它的方法的调用都会返回 CFG_NODATASOURCE。 

### 3. 创建配置节

```cpp
HRESULT CfgCreateSection(BSTR p_bstrPSec, BSTR p_bstrNewSec,short* p_psRet);
```

- **p_bstrPSec**：父配置节，如果为空字符串，表示无父配置节，新的配置节将作为最根部的配置节。当有多层配置节时，以"\"分开表示它们的路径。例如 "DEV\CashDispenser\Comm" 字符串表示 DEV 配置节下的 CashDispenser 子节的子节 Comm；

- **p_bstrNewSec**：新的子配置节的名字;

- > **返回值**：
  >
  > - CFG_SUCCESS：成功；
  > - CFG_UPDATENEEDCOMMIT：需要调用 CfgCommit 更新才能生效；
  > - CFG_NODATASOURCE：没有数据源打开；
  > - CFG_INVALIDSECTION：错误的配置节，即 p_bstrPSec 无效；
  > - CFG_FAIL：其它错误。 

#### 3.1 删除配置节

```cpp
HRESULT CfgDeleteSection(BSTR p_bstrPSec, BSTR p_bstrDelSec, short* p_psRet);
```

- **p_bstrPSec **：要删除的配置节的上一节的配置节路径，对于 INI 文件，此参数将被忽略**；**

- **p_bstrDelSec**：要删除的配置节的路径。

- > **返回值**：
  >
  > - CFG_SUCCESS：成功；
  > - CFG_UPDATENEEDCOMMIT：需要调用 CfgCommit 更新才能生效；
  > - CFG_NODATASOURCE：没有数据源打开；
  > - CFG_INVALIDSECTION：错误的配置节，即 **p_bstrPSec** 或 **p_bstrDelSec** 不存在；
  > - CFG_FAIL：其它错误。 

### 4. 获取配置键

```cpp
HRESULT CfgGetStringValue(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName,
	BSTR* p_pbstrValue);

HRESULT CfgGetIntValue(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName,
	long* p_plValue);

HRESULT CfgGetBinaryValue(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName,
	VARIANT* p_pvarValue);
```

- **p_bstrSection**：父配置节的路径；

- **p_bstrKeyName**：配置键的名；

- > **返回值**：如果当前配置键存在，将返回相应的配置值,  如果配置不存在，将返回空值。
  >
  > VARIANT 为 VT_EMPTY。

#### 4.1 设置配置键

```cpp
HRESULT CfgSetStringValue(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName,
	BSTR p_bstrValue,
	short* p_psRet );


HRESULT CfgSetIntValue(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName,
	long p_lValue,
	short* p_psRet );

HRESULT CfgSetBinaryValue(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName, 
	VARIANT p_varValue,
	short* p_psRet );
```

- **p_bstrSection**：要更新的配置键所在的配置节；

- **p_bstrKeyName**：配置键的名，如果它不存在，将在当前配置节下自动新建；

- **p_varValue, p_bstrValue, p_lValue**：要更新的值；

- > **返回值**：
  >
  > - CFG_SUCCESS：成功；
  > - CFG_UPDATENEEDCOMMIT：需要调用 CfgCommit 更新才能生效，INI 及系统注册表都是立即更新生效，只有 XML 需要确认；
  > - CFG_NODATASOURCE：没有数据源打开；
  > - CFG_INVALIDSECTION：错误的配置节，即 p_bstrSection 无效；
  > - CFG_FAIL：其它错误。 
  >
  > 注：根据配置数据源的不同，有些能立即更新到文件，如数据库类，注册表类，INI 文件类，配置数据立即更新到文件不会有什么性能上的损失。但对于 **XML 文件**，需要将 XML **一次装载进内存，进行解析**，在内存中进行所有更新后再一次写入文件。所以对于 XML 文件的配置数据的更新一般都会返回 CFG_UPDATENEEDCOMMIT，直到用户调用 CfgCommit 才将数据写入文件。 

### 5. 获取多个配置键值

```cpp
HRESULT CfgGetValues(BSTR p_bstrSection, VARIANT p_bstrKeyNames, VARIANT* p_pvarValue); 
```

- **p_bstrPSection**：配置键所在的配置节；

- **p_bstrKeyNames**：多个配置键的数组；

- > **返回值**：
  >
  > 返回与 p_bstrKeyNames 相同维数的数组，如果 p_bstrKeyNames 数组里第 n 项元素指向的配置键存在，返回数组中的第 n 项无素即为相应的配置值,  如果配置键不存在，即为空值。 

#### 5.1 设置多个配置键的值

```cpp
HRESULT CfgSetValues(BSTR p_bstrSection, VARIANT p_bstrKeyNames, VARIANT p_varValue, short* p_psR);
```

- **p_bstrPSection**：多个配置键所在的配置节；

- **p_bstrKeyNames**：多个配置键的数组；

- **p_varValue**：对应的每个配置键的值的数组。

- > **返回值**：
  >
  > - CFG_SUCCESS：成功。 
  > - CFG_UPDATENEEDCOMMIT：需要调用 CfgCommit 更新才能生效。
  > - CFG_NODATASOURCE：没有数据源打开。 
  > - CFG_INVALIDSECTION：错误的配置节，即 p_bstrSection 无效。 
  > - CFG_FAIL：其它错误。 

### 6. 新建配置键

```cpp
HRESULT CfgCreateKey(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName, 
	VARIANT p_varInitValue,
	short* p_psRet);
```

- **p_bstrSection**：父配置节；

- **p_bstrKeyName**：新的配置键的键名；

- **p_varInitValue**：初始化的值，可为数值，字符串或二进制。如为其它无法转换，将返回 CFG_INVALIDDATATYPE；

- > **返回值**：
  >
  > - CFG_SUCCESS：成功;
  > - CFG_UPDATENEEDCOMMIT：需要调用 CfgCommit 更新才能生效;
  > - CFG_NODATASOURCE：没有数据源打开；
  > - CFG_INVALIDSECTION：错误的配置节，即 p_bstrSection 无效；
  > - CFG_INVALIDDATATYPE：错误的数据类型；
  > - CFG_FAIL：其它错误。
  >
  > 注：在每个配置节下，配置键不能重名。在调用CfgCreateKey时，如果键名已经存在，将返回CFG_SUCCESS，并保持原有的值不变。

#### 6.1 删除配置键

```cpp
HRESULT CfgDeleteKey(
	BSTR p_bstrSection, 
	BSTR p_bstrKeyName,
	short* p_psRet);
```

- **p_bstrSection**：要删除的配置键所在的父配置节；

- **p_bstrKeyName**：要删除的配置键的名；

- > **返回值**：
  >
  > - CFG_SUCCESS：成功；
  > - CFG_UPDATENEEDCOMMIT：需要调用 CfgCommit 更新才能生效；
  > - CFG_NODATASOURCE：没有数据源打开；
  > - CFG_INVALIDSECTION：错误的配置节，即 p_bstrSection 无效；
  > - CFG_INVALIDKEY：无效的键名；
  > - CFG_FAIL：其它错误。 

### 7.  枚举配置节

```cpp
HRESULT CfgEnumChilds(BSTR p_bstrPSection, ICfgCollection** p_pvarCollection);
```

- **p_bstrPSection**：要枚举的配置节；

- > **返回值**：返回指定配置节下的所有子配置节及键的集合 ICfgCollection 接口。 

### 8. 确认更新

更新所有配置，并写入文件。

```cpp
HRESULT CfgCommit(short* p_psRet);
```

- > 返回值：
  >
  > - CFG_SUCCESS：成功；
  > - CFG_NODATASOURCE：没有数据源打开；
  > - CFG_FAIL：其它错误。
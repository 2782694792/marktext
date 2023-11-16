# Linux

## 头文件

```cpp
// inifile.h: interface for the CIniFile class.
//
//////////////////////////////////////////////////////////////////////

#if !defined(AFX_INIFILE_H__F450C4AD_78A5_462E_A588_9A44D6769614__INCLUDED_)
#define AFX_INIFILE_H__F450C4AD_78A5_462E_A588_9A44D6769614__INCLUDED_

#include <iostream>
using namespace std;

#include "typedef.h"

#define  MAX_CFG_LENGTH	256
enum CFG_DATA_TYPE	
{
	TYPE_SECTION = 1,
	TYPE_KEY,
	TYPE_VALUE,
	TYPE_COMMENT,
	TYPE_OTHERS,
};

struct tGrgCfgData 
{
	CFG_DATA_TYPE eType;
	char acData[MAX_CFG_LENGTH];
	int iValidLength;

	char acSection[MAX_CFG_LENGTH];
	char acSectionComment[MAX_CFG_LENGTH];
	char acKeyName[MAX_CFG_LENGTH];
	char acValue[MAX_CFG_LENGTH];
	char acComment[MAX_CFG_LENGTH];
	int  iValue;
};

// typedef list<tGrgCfgData> MYLIST;

class CIniFile  
{
public:
	CIniFile();
	virtual ~CIniFile();

	int iLoadFile(char *p_pcFileName);

	int iGetIntValue(const char *p_pcSection, const char *p_pcKeyName, int& p_iKeyValue);
	int iGetStringValue(const char *p_pcSection, const char *p_pcKeyName, char *p_pcKeyValue, UINT& p_uiLength);

    int iSetIntValue(const char *p_pcSection, const char *p_pcKeyName, long p_lKeyValue);
    int iSetStringValue(const char *p_pcSection, const char *p_pcKeyName, char *p_pcKeyValue, UINT p_uiLength);

private:

	tGrgCfgData *m_psData;
	char* pcTrim(char *p_pcInput);	// 去除不可见的控制字符
        bool    m_bValid;
	char	m_acFileName[MAX_CFG_LENGTH];
	int		m_iFileHandle;
	char	m_acSection[MAX_CFG_LENGTH];
	char	m_acKey[MAX_CFG_LENGTH];

	int		m_iCount;
};

#endif // !defined(AFX_INIFILE_H__F450C4AD_78A5_462E_A588_9A44D6769614__INCLUDED_)
```

## cpp 文件

```cpp
// inifile.cpp: implementation of the CIniFile class.
//
//////////////////////////////////////////////////////////////////////
#include "IniFile.h"
#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <string.h>
#include <sys/ioctl.h>
#include <stdio.h> 
#include <unistd.h>
#include <stdlib.h>


//#define MAX_DATA_COUNT	0xFFFF
#define MAX_DATA_COUNT	4096
//////////////////////////////////////////////////////////////////////
// Construction/Destruction
//////////////////////////////////////////////////////////////////////

CIniFile::CIniFile()
{
	memset(m_acFileName, 0, sizeof(m_acFileName) );
	m_iFileHandle = 0;
	m_psData = new tGrgCfgData[MAX_DATA_COUNT];
	memset(m_psData, 0, MAX_DATA_COUNT * sizeof(tGrgCfgData));
	m_iCount = 0;
}

CIniFile::~CIniFile()
{
	if (NULL != m_psData)
	{
		delete []m_psData;
	}
}

int CIniFile::iLoadFile(char *p_pcFileName)
{

        int l_iLen = 0;
	m_iFileHandle = open(p_pcFileName, O_RDWR);
	strcpy(m_acFileName, p_pcFileName);
	if (-1 == m_iFileHandle)
	{
		return 1;
	}

//	int l_iFileSize = _filelength(m_iFileHandle);
	int l_iFileSize = 0;
	struct stat l_sFileState;
	if (0 == fstat(m_iFileHandle, &l_sFileState))
	{
		l_iFileSize = l_sFileState.st_size;
	}
	char *l_pcFile = new char[l_iFileSize + 10];
	if (NULL == l_pcFile)
	{
		return 1;
	}

	int l_iRead = read(m_iFileHandle, l_pcFile, l_iFileSize);
	tGrgCfgData l_sData;
	memset(&l_sData, 0, sizeof(l_sData) );
	int l_iBegin = 0;
	int l_iEnd = 0;
	int l_iLength = 0;
	int l_iCount = 0;

	if ('\n' != l_pcFile[l_iRead-1])
	{
		l_pcFile[l_iRead++] = '\r';
		l_pcFile[l_iRead++] = '\n';
		lseek(m_iFileHandle, 0, SEEK_END);
                l_iLen = write(m_iFileHandle, "\r\n", 2);
                if(2 != l_iLen)
                {
                    close(m_iFileHandle);
                    delete l_pcFile;
                    return 1;
                }
	}

	close(m_iFileHandle);
	memset(m_psData, 0, MAX_DATA_COUNT * sizeof(tGrgCfgData));
	m_iCount = 0;

	for (int i=0; i<l_iRead; i++)
	{
		if ('\n' == l_pcFile[i])
		{
			l_iEnd = i;
			if (0 != l_iLength)
			{
				if (l_iLength > MAX_CFG_LENGTH)
				{
					return 1;
				}
				memset(l_sData.acData, 0, sizeof(l_sData.acData));
			//	memcpy(l_sData.acData, &l_pcFile[l_iBegin+1], l_iLength + 1);
				memcpy(l_sData.acData, &l_pcFile[0 == l_iBegin ? 0 : (l_iBegin+1)], l_iLength + 1);
				l_sData.iValidLength = l_iLength + 1;
				l_iLength = 0;

				char *l_pcBegin = strchr(l_sData.acData, '[');
				char *l_pcEnd = strchr(l_sData.acData, ']');
				char *l_pcEquBegin = strchr(l_sData.acData, '=');
				char *l_pcComment1 = strchr(l_sData.acData, ';');
				char *l_pcComment2 = strchr(l_sData.acData, ';');
				if (NULL != l_pcBegin 
					&& NULL != l_pcEnd 
					&& l_pcEnd - l_pcBegin > 0 
					)
				{
					l_sData.eType = TYPE_SECTION;
					memset(m_acSection, 0, sizeof(m_acSection));
					memcpy(m_acSection, &l_sData.acData[l_pcBegin - l_sData.acData + 1], 
						l_pcEnd - l_pcBegin - 1);
					pcTrim(m_acSection);
					memset(l_sData.acSection, 0, sizeof(l_sData.acSection));
					memcpy(l_sData.acSection, m_acSection, sizeof(m_acSection));
					memset(l_sData.acSectionComment, 0, sizeof(l_sData.acSectionComment));

					if (NULL != l_pcComment1
						&& l_pcComment1 > l_pcEnd	// 注释必须在后面
						)
					{
						memcpy(l_sData.acSectionComment, &l_sData.acData[l_pcComment1 - l_sData.acData], 
							l_sData.iValidLength - (l_pcComment1 - l_sData.acData));
					}
					if (NULL != l_pcComment2
						&& l_pcComment2 > l_pcEnd	// 注释必须在后面						
						)
					{
						memcpy(l_sData.acSectionComment, &l_sData.acData[l_pcComment2 - l_sData.acData], 
							l_sData.iValidLength - (l_pcComment2 - l_sData.acData));
					}
				}
				else if (NULL != l_pcEquBegin)
				{
					l_sData.eType = TYPE_KEY;
					memset(m_acKey, 0, sizeof(m_acKey));
					memcpy(m_acKey, l_sData.acData, l_pcEquBegin - l_sData.acData);
					strcpy(l_sData.acSection, m_acSection);
					pcTrim(m_acKey);
					strcpy(l_sData.acKeyName, m_acKey);

					memset(l_sData.acValue, 0, sizeof(l_sData.acValue));
					memcpy(l_sData.acValue, &l_sData.acData[l_pcEquBegin - l_sData.acData + 1], 
						l_sData.iValidLength - (l_pcEquBegin - l_sData.acData) - 1);
					pcTrim(l_sData.acValue);
					l_sData.iValue = atoi(l_sData.acValue);

					if (NULL != l_pcComment1
						&& l_pcComment1 > l_pcEquBegin	// 注释必须在后面
						)
					{
						memset(l_sData.acComment, 0, sizeof(l_sData.acComment));
						memcpy(l_sData.acComment, &l_sData.acData[l_pcComment1 - l_sData.acData], 
							l_sData.iValidLength - (l_pcComment1 - l_sData.acData));
						memset(l_sData.acValue, 0, sizeof(l_sData.acValue));
						memcpy(l_sData.acValue, &l_sData.acData[l_pcEquBegin - l_sData.acData + 1], 
							l_pcComment1 - l_pcEquBegin - 1);
						pcTrim(l_sData.acValue);
						l_sData.iValue = atoi(l_sData.acValue);
					}
					if (NULL != l_pcComment2
						&& l_pcComment2 > l_pcEquBegin	// 注释必须在后面
						)
					{
						memset(l_sData.acComment, 0, sizeof(l_sData.acComment));
						memcpy(l_sData.acComment, &l_sData.acData[l_pcComment2 - l_sData.acData], 
							l_sData.iValidLength - (l_pcComment2 - l_sData.acData));
						memset(l_sData.acValue, 0, sizeof(l_sData.acValue));
						memcpy(l_sData.acValue, &l_sData.acData[l_pcEquBegin - l_sData.acData + 1], 
							l_pcComment2 - l_pcEquBegin - 1);
						pcTrim(l_sData.acValue);
						l_sData.iValue = atoi(l_sData.acValue);
					}
				}
				else
				{
					l_sData.eType = TYPE_OTHERS;
				}

			}
			else
			{
				memset(l_sData.acData, 0, sizeof(l_sData.acData));
				memcpy(l_sData.acData, "\n", 1);
				l_sData.iValidLength = 1;
				l_sData.eType = TYPE_OTHERS;
			}
			memcpy(&m_psData[m_iCount++], &l_sData, sizeof(l_sData));
			memset(&l_sData, 0, sizeof(l_sData) );
			l_iBegin = i;
			l_iCount++;
		}
		else
		{
			l_iLength++;
		}
	}
	delete []l_pcFile;

	return 0;
}

int CIniFile::iGetIntValue(const char *p_pcSection, const char *p_pcKeyName, int& p_iKeyValue)
{
	tGrgCfgData l_sData;
	memset(&l_sData, 0, sizeof(l_sData) );
	for (int k=0; k != m_iCount; k++)
	{
		memcpy(&l_sData, &m_psData[k], sizeof(l_sData));
		if (TYPE_KEY == l_sData.eType
			&& 0 == strcasecmp(p_pcSection, l_sData.acSection)
			&& 0 == strcasecmp(p_pcKeyName, l_sData.acKeyName)
			)
		{
			p_iKeyValue = l_sData.iValue;
			return 0;
		}
	}

	return 1;
}

int CIniFile::iGetStringValue(const char *p_pcSection, const char *p_pcKeyName, char *p_pcKeyValue, UINT& p_uiLength)
{
	tGrgCfgData l_sData;
	memset(&l_sData, 0, sizeof(l_sData) );
	for (int k=0; k != m_iCount; k++)
	{
		memcpy(&l_sData, &m_psData[k], sizeof(l_sData));
		if (TYPE_KEY == l_sData.eType
			&& 0 == strcasecmp(p_pcSection, l_sData.acSection)
			&& 0 == strcasecmp(p_pcKeyName, l_sData.acKeyName)
			)
		{
			p_uiLength = p_uiLength < strlen(l_sData.acValue) ? p_uiLength : strlen(l_sData.acValue);
			memcpy(p_pcKeyValue, l_sData.acValue, p_uiLength);
			return 0;
		}
	}
	
	return 1;
}

int CIniFile::iSetIntValue(const char *p_pcSection, const char *p_pcKeyName, long p_lKeyValue)
{
	char l_acKeyValue[MAX_CFG_LENGTH] = {0};
	sprintf(l_acKeyValue, "%d", p_lKeyValue);
	int l_iRet = iSetStringValue(p_pcSection, p_pcKeyName, l_acKeyValue, strlen(l_acKeyValue));
        if(0 != l_iRet)
        {
		return 1;
    	}

	//虽然好罗嗦，但是还是按照整个代码的原意来
	//其实完全可以在iGetIntValue里面用字符串转int即可
    	tGrgCfgData l_sData;
    	memset(&l_sData, 0, sizeof(l_sData) );
    	for (int k=0; k != m_iCount; k++)
    	{
		memcpy(&l_sData, &m_psData[k], sizeof(l_sData));
		if (TYPE_KEY == l_sData.eType
	    	&& 0 == strcasecmp(p_pcSection, l_sData.acSection)
	    	&& 0 == strcasecmp(p_pcKeyName, l_sData.acKeyName)
	    	)
		{
	    		m_psData[k].iValue = p_lKeyValue;
	    		return 0;
		}
    	}

    	return 1;
}

int CIniFile::iSetStringValue(const char *p_pcSection, const char *p_pcKeyName, char *p_pcKeyValue, UINT p_uiLength)
{
	// 如果存在就不能重写
	char l_acKeyValue[MAX_CFG_LENGTH] = {0};
	UINT l_uiLength = MAX_CFG_LENGTH;
	if (0 == iGetStringValue(p_pcSection, p_pcKeyName, l_acKeyValue, l_uiLength))
	{
		pcTrim(l_acKeyValue);
		if (0 == strcasecmp(l_acKeyValue, p_pcKeyValue))
		{
			return 0;
		}
	}

	tGrgCfgData l_sData;
	memset(&l_sData, 0, sizeof(l_sData) );
        //iLoadFile(m_acFileName);
        m_iFileHandle = open(m_acFileName, O_RDWR | O_TRUNC);
        //m_iFileHandle = open(m_acFileName, O_RDWR);
	if (-1 == m_iFileHandle)
	{
		return 1;
	}
	
	BOOL	l_bSectionExist = FALSE;
	BOOL	l_bKeyExist = FALSE;
	BOOL	l_bKeyWritten = FALSE;
	tGrgCfgData l_asDataToInsert[20];
	memset(l_asDataToInsert, 0, sizeof(l_asDataToInsert) );
	int		n = 0;
	int		l_iInsetIndex = 0;

        int l_iLen = 0;

        // write 函数必须要检查返回值
        // write 函数不能百分百保证成功
	   int j = 0, h;
        bool l_bFoundKey = FALSE;
        bool l_bFoundSection = FALSE;

        for (j = 0; j != m_iCount; ++j)
        {
             if(0 == strcasecmp(p_pcSection, m_psData[j].acSection))
             {
                 l_bFoundSection = TRUE;
                 break;
             }
        }

        for (j = 0; j != m_iCount; ++j)
        {
             if(0 == strcasecmp(p_pcSection, m_psData[j].acSection) && 0 == strcasecmp(p_pcKeyName, m_psData[j].acKeyName))
             {
                 l_bFoundKey = TRUE;
                 break;
             }
        }


        try
        {
            lseek(m_iFileHandle, 0, SEEK_SET);
            int k=0;
            for (k=0; k != m_iCount; k++)
            {
                    memcpy(&l_sData, &m_psData[k], sizeof(l_sData));
                    if (TYPE_KEY == l_sData.eType)
                    {
                            l_bKeyExist = (0 == strcasecmp(p_pcKeyName, l_sData.acKeyName)) ? TRUE : FALSE;
                            if (l_bSectionExist && l_bKeyExist)
                            {
                                    memset(l_sData.acValue, 0, sizeof(l_sData.acValue));
                                    memcpy(l_sData.acValue, p_pcKeyValue, p_uiLength < sizeof(l_sData.acValue) ? p_uiLength : sizeof(l_sData.acValue) );
                                    memcpy(&m_psData[k], &l_sData, sizeof(l_sData));
                                    
                            }

                            // 写键值
                            l_iLen = write(m_iFileHandle, l_sData.acKeyName, strlen(l_sData.acKeyName));
                            if(strlen(l_sData.acKeyName) != l_iLen)
                            {
                                throw 1;
                            }

                            l_iLen = write(m_iFileHandle, " = ", strlen(" = ") );
                            if(strlen(" = ") != l_iLen)
                            {
                                throw 1;
                            }

                            l_iLen = write(m_iFileHandle, l_sData.acValue, strlen(l_sData.acValue));
                            if(strlen(l_sData.acValue) != l_iLen)
                            {
                                throw 1;
                            }

                            if (0 == strlen(l_sData.acComment) )
                            {
                                    l_iLen = write(m_iFileHandle, "\r\n", strlen("\r\n") );
                                    if(strlen("\r\n") != l_iLen)
                                    {
                                        throw 1;
                                    }
                            }
                            else
                            {
                                    l_iLen = write(m_iFileHandle, l_sData.acComment, strlen(l_sData.acComment));
                                    if(strlen(l_sData.acComment) != l_iLen)
                                    {
                                        throw 1;
                                    }
                            }
                    }
                    else if (TYPE_SECTION == l_sData.eType)
                    {
                            l_bSectionExist = (0 == strcasecmp(p_pcSection, l_sData.acSection)) ? TRUE : FALSE;
                            // 写Section
                            l_iLen = write(m_iFileHandle, "[", strlen("[") );
                            if(strlen("[") != l_iLen)
                            {
                                throw 1;
                            }

                            l_iLen = write(m_iFileHandle, l_sData.acSection, strlen(l_sData.acSection));
                            if(strlen(l_sData.acSection) != l_iLen)
                            {
                                throw 1;
                            }

                            l_iLen = write(m_iFileHandle, "]", strlen("]") );
                            if(strlen("]") != l_iLen)
                            {
                                throw 1;
                            }

                            if (0 == strlen(l_sData.acSectionComment) )
                            {
                                    l_iLen = write(m_iFileHandle, "\r\n", strlen("\r\n") );
                                    if(strlen("\r\n") != l_iLen)
                                    {
                                        throw 1;
                                    }
                            }
                            else
                            {
                                    l_iLen = write(m_iFileHandle, l_sData.acSectionComment, strlen(l_sData.acSectionComment));
                                    if(strlen(l_sData.acSectionComment) != l_iLen)
                                    {
                                        throw 1;
                                    }
                            }
                    }
                    else
                    {
                            // 有Section，但没有Key，插入一个结构体
                            if (l_bSectionExist && !l_bKeyWritten && (FALSE == l_bFoundKey &&  TRUE== l_bFoundSection ))
                            {
                                    l_asDataToInsert[n].eType = TYPE_KEY;
                                    strcpy(l_asDataToInsert[n].acSection, p_pcSection);
                                    strcpy(l_asDataToInsert[n].acKeyName, p_pcKeyName);
                                    memcpy(l_asDataToInsert[n].acValue, p_pcKeyValue, p_uiLength < sizeof(l_sData.acValue) ? p_uiLength : sizeof(l_sData.acValue) );

                                    l_iInsetIndex = k;
                                    l_iLen = write(m_iFileHandle, l_asDataToInsert[n].acKeyName, strlen(l_asDataToInsert[n].acKeyName));
                                    if(strlen(l_asDataToInsert[n].acKeyName) != l_iLen)
                                    {
                                        throw 1;
                                    }

                                    l_iLen = write(m_iFileHandle, " = ", strlen(" = ") );
                                    if(strlen(" = ") != l_iLen)
                                    {
                                        throw 1;
                                    }

                                    l_iLen = write(m_iFileHandle, l_asDataToInsert[n].acValue, strlen(l_asDataToInsert[n].acValue));
                                    if(strlen(l_asDataToInsert[n].acValue) != l_iLen)
                                    {
                                        throw 1;
                                    }

                                    l_iLen = write(m_iFileHandle, "\r\n", strlen("\r\n") );
                                    if(strlen("\r\n") != l_iLen)
                                    {
                                        throw 1;
                                    }

                                    l_bKeyWritten = TRUE;
                                    n++;
                            }

                            // 其他的不写
                            l_iLen = write(m_iFileHandle, l_sData.acData, strlen(l_sData.acData));
                            if(strlen(l_sData.acData) != l_iLen)
                            {
                                throw 1;
                            }
                    }
            }

            if (FALSE == l_bFoundKey &&  FALSE == l_bFoundSection )
            {
                    // 没有Section，也没有key

                    // 先插入Section
                    l_asDataToInsert[n].eType = TYPE_SECTION;
                    strcpy(l_asDataToInsert[n].acSection, p_pcSection);
                    // 写Section
                    l_iLen = write(m_iFileHandle, "\r\n", strlen("\r\n") );
                    if(strlen("\r\n") != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, "[", strlen("[") );
                    if(strlen("[") != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, l_asDataToInsert[n].acSection, strlen(l_asDataToInsert[n].acSection));
                    if(strlen(l_asDataToInsert[n].acSection) != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, "]", strlen("]") );
                    if(strlen("]") != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, "\r\n", strlen("\r\n") );
                    if(strlen("\r\n") != l_iLen)
                    {
                        throw 1;
                    }

                    // 写Key
                    n++;
                    l_asDataToInsert[n].eType = TYPE_KEY;
                    strcpy(l_asDataToInsert[n].acSection, p_pcSection);
                    strcpy(l_asDataToInsert[n].acKeyName, p_pcKeyName);
                    memcpy(l_asDataToInsert[n].acValue, p_pcKeyValue,
                            p_uiLength < sizeof(l_sData.acValue) ? p_uiLength : sizeof(l_sData.acValue) );
                    l_iInsetIndex = k;

                    l_iLen = write(m_iFileHandle, l_asDataToInsert[n].acKeyName, strlen(l_asDataToInsert[n].acKeyName));
                    if(strlen(l_asDataToInsert[n].acKeyName) != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, " = ", strlen(" = ") );
                    if(strlen(" = ") != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, l_asDataToInsert[n].acValue, strlen(l_asDataToInsert[n].acValue));
                    if(strlen(l_asDataToInsert[n].acValue) != l_iLen)
                    {
                        throw 1;
                    }

                    l_iLen = write(m_iFileHandle, "\r\n", strlen("\r\n") );
                    if(strlen("\r\n") != l_iLen)
                    {
                        throw 1;
                    }

                    n++;
            }

            // 更新缓冲区
            if (0 != l_iInsetIndex)
            {
                    l_asDataToInsert[n].eType = TYPE_OTHERS;
                    strcpy(l_asDataToInsert[n].acData, "\r\n");
                    l_asDataToInsert[n].iValidLength = 2;
                    n++;

                    if (m_iCount <= l_iInsetIndex)
                    {
                            // append
                            memcpy(&m_psData[l_iInsetIndex], l_asDataToInsert, n * sizeof(tGrgCfgData));
                    }
                    else
                    {
                            // insert
                            //memcpy(&m_psData[l_iInsetIndex + n], &m_psData[l_iInsetIndex], (m_iCount - l_iInsetIndex) * sizeof(tGrgCfgData));
                            //memcpy(&m_psData[l_iInsetIndex], l_asDataToInsert, n * sizeof(tGrgCfgData));
							
							unsigned int l_uiLenTmp = (m_iCount - l_iInsetIndex) * sizeof(tGrgCfgData);
							unsigned char *l_pTmp = (unsigned char *)malloc(l_uiLenTmp);
							memset(l_pTmp, 0x00, l_uiLenTmp);


							memcpy(l_pTmp, &m_psData[l_iInsetIndex], l_uiLenTmp);
							memset(&m_psData[l_iInsetIndex], 0x00, (MAX_DATA_COUNT - l_iInsetIndex) * sizeof(tGrgCfgData));


							memcpy(&m_psData[l_iInsetIndex + n], l_pTmp, l_uiLenTmp);

							memcpy(&m_psData[l_iInsetIndex], l_asDataToInsert, n * sizeof(tGrgCfgData));

							free(l_pTmp);
                    }

                    m_iCount += n;
            }

        }
        catch(int e)
        {
            close(m_iFileHandle);

            system("touch error.txt");
            system("echo write file error > error.txt");

            return 1;
        }
	
//	_commit(m_iFileHandle);
	close(m_iFileHandle);
	return 0;
}

char* CIniFile::pcTrim(char *p_pcInput)
{
	char *l_pcBack = new char[strlen(p_pcInput)];
	int j = 0;
	for (int i=0; i<strlen(p_pcInput); i++)
	{
		int l_iTemp = p_pcInput[i];
		if ((l_iTemp > 0x20) && (l_iTemp <= 0x7F))
		{
			l_pcBack[j++] = p_pcInput[i]; 
		}
	}
	
	memset(p_pcInput, 0, strlen(p_pcInput));
	memcpy(p_pcInput, l_pcBack, j);
	delete []l_pcBack;
	return p_pcInput;
}
```


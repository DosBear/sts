
###ВЭД-Склад


#Изменение идентификатора у ДО-1
VAR ('sDocumentID' , String, INPUTTEXT ('Изменение DocumentID', 'Введите значение'));
IF (sDocumentID <> '',
  EXECUTESQL ('STS_DB', 'UPDATE KRD_MAIN SET DOCUMENTID=' +char(39)+ sDocumentID +char(39)+ ' WHERE PLACEID=' + KRD_MAIN.PLACEID + ' AND ID=' +KRD_MAIN.ID);
); // IF - //

EXECUTESQL ('STS_DB', 'UPDATE KRD_MAIN SET DOCUMENTID=' +char(39)+ '29201C91-C52A-4EFC-806E-AACC5AC4096A' +char(39)+ ' WHERE PLACEID=' + KRD_MAIN.PLACEID + ' AND ID=' +KRD_MAIN.ID);

#При восстановлении протокола нужно запрашивать следующие сообщения
cmn.13009, cmn.13010, cmn.13020, cmn.13021

#Изменение номер у акта
UPDATE KRD_DOP SET DOC_ORDER_NUMBER=2 WHERE DOC_NO='10612070/281218/0000001'
update krd_dop set DOC_REG_STATUS='3' where id=1215668037;

#Регистрация КомАкта
UPDATE KRD_DOP SET DOC_REG_STATUS='3' WHERE DOC_NO='0001067'

UPDATE KRD_DOP SET DOC_REG_STATUS='3', DOC_REG_NO='10612130/100418/0008605', DOC_REG_DATETIME='15.04.2018 10:20:00, DOC_INSPECTOR_FIO='Иванов Н.В.', DOC_INSPECTOR_LNP='000'
WHERE DOC_NO='0000608'

#Регистрация ДО-1
ShowMessage('Регистрация для ДО1: ' + KRD_MAIN.NBD);
EXECUTESQL ('STS_DB', 'UPDATE KRD_MAIN SET MC_STATUS_BD = ' +char(39)+ '3' +char(39)+ ', STATUS_EPS = ' +char(39)+ 'ДО-1 зарегистрирована' +char(39)+ '  WHERE MAIN_ID=' + KRD_MAIN.MAIN_ID + ' AND PLACEID=' + KRD_MAIN.PLACEID);
GLOBALREFRESH ();
SHOWMESSAGE('Обработка документов завершена.')

#Регистрация ДО-2
ShowMessage('Регистрация всех ДО2 для ДО1' + KRD_MAIN.NBD);
EXECUTESQL ('STS_DB', 'UPDATE RELEASE SET MC_STATUS = ' +char(39)+ '3' +char(39)+ '  WHERE ID=' + KRD_MAIN.MAIN_ID + ' AND PLACEID=' + KRD_MAIN.PLACEID);
GLOBALREFRESH ();
SHOWMESSAGE('Обработка документов завершена.')


#Решение проблемы с BLOB size
[Database]
BlobsToCache=4024


#ПЕРЕНОС СПРАВОЧНИКОВ

INSERT INTO [STS_2019].[dbo].[COMMODIT]
SELECT * FROM [STS_2018_OKT].[dbo].[COMMODIT]

INSERT INTO [STS_2019].[dbo].[ORGANIZ]
SELECT * FROM [STS_2018_OKT].[dbo].[ORGANIZ]


#Вывод всех скриптов ВЭД-Склада
EXECUTESCRIPT (INCLUDETRAILINGBACKSLASH(PROGRAMPATH()) + "procdocs\refresh_eps.prd");
RefreshEps();
GLOBALREFRESH ();
SHOWMESSAGE('Обработка документов завершена.')
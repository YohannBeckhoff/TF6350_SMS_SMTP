Note pour l'utilisation du TF6350
# Special Gmail

- Aller sur [myaccount.google.com](https://myaccount.google.com)
- Sécurité → Validation en deux étapes (doit être activée)
- Sécurité → **Mots de passe des applications**
- Générer un mot de passe pour "Mail"
- Utiliser ce code à 16 caractères dans `sPassword`

Maintenant génère ton App Password :

1. Va sur : **myaccount.google.com/apppasswords**
2. Connecte-toi si demandé
3. Dans le champ **"Nom de l'application"**, tape par exemple `BeckhoffPLC`
4. Clique sur **Créer**
5. Google te donne un code à **16 caractères** → note-le bien, il ne s'affiche qu'une fois

# GVL_SMTP

```Pascal
VAR_GLOBAL
	//A completer par vos identifiants
	sSmtpServer:STRING:='smtp.gmail.com';
	sUsername:STRING:='xxxx@gmail.com';
	sPassword:STRING:='abcd efgh ijkl mnop'; //Vrai code envoyé par mail
	sFrom:STRING:='xxxx@gmail.com';
	sTo:STRING:='xxxx@beckhoff.com';
END_VAR
```
# Code SMTP

```pascal
PROGRAM SMTP
VAR
	fbSendMail: FB_SmtpV3;
	sMessage: STRING := 'Hello Beckhoff';
	R_Edge: R_TRIG;
	bStart: BOOL;
	bBusy: BOOL;
	bError: BOOL;
	nErrID: UDINT;
	bSend: BOOL;
	nErr: UDINT;
	nMailCounter: UDINT;
END_VAR

fbSendMail(
	sNetId:= '', 
	sSmtpServer:= sSmtpServer, 
	sUsername:= sUsername, 
	sPassword:= sPassword, 
	nEncryption:= 1, 
	sFrom:= sFrom, 
	sTo:= sTo, 
	sCc:= , 
	sBcc:= , 
	sSubject:= 'Email from your Beckhoff PLC', 
	pMessage:= ADR(sMessage), 
	cbMessage:= SIZEOF(sMessage), 
	bExecute:= bStart, 
	tTimeout:= DEFAULT_ADS_TIMEOUT, 
	bBusy=> bBusy, 
	bError=> bError, 
	nErrId=> nErrId);	
	
IF NOT bBusy  AND bStart THEN
bStart := FALSE;
END_IF 

```


# Code SMTPFull

```pascal
PROGRAM SMTPFull
VAR
	fbSmtpFull    : FB_SmtpV3_Full;
	sMessage_HTML : STRING(5000) := '<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"><html><head><title>Alerte PLC</title></head><body style="margin:0;padding:0;background-color:#f4f4f4;font-family:Arial,sans-serif;"><table width="100%" cellpadding="0" cellspacing="0" style="background-color:#f4f4f4;padding:30px 0;"><tr><td align="center"><table width="600" cellpadding="0" cellspacing="0" style="background-color:#ffffff;border-radius:8px;overflow:hidden;box-shadow:0 2px 8px rgba(0,0,0,0.1);"><tr><td style="background-color:#006DB7;padding:24px 32px;"><h1 style="margin:0;color:#ffffff;font-size:20px;">&#9881; Beckhoff PLC Notification</h1></td></tr><tr><td style="padding:32px;"><h2 style="color:#333333;font-size:18px;margin-top:0;">Bonjour,</h2><p style="color:#555555;font-size:15px;line-height:1.6;">Cet e-mail a ete envoye automatiquement depuis votre automate Beckhoff.</p><table width="100%" cellpadding="0" cellspacing="0" style="background-color:#f0f7ff;border-left:4px solid #006DB7;border-radius:4px;margin:24px 0;"><tr><td style="padding:16px;"><p style="margin:0;color:#333;font-size:14px;"><b>Machine :</b> PLC-01</p><p style="margin:8px 0 0;color:#333;font-size:14px;"><b>Statut :</b> <span style="color:#28a745;">&#10003; Operationnel</span></p><p style="margin:8px 0 0;color:#333;font-size:14px;"><b>Heure :</b> 14:32:05</p></td></tr></table><p style="color:#555555;font-size:15px;line-height:1.6;">Veuillez ne pas repondre a cet e-mail, il est envoye automatiquement.</p></td></tr><tr><td style="background-color:#f4f4f4;padding:16px 32px;border-top:1px solid #eeeeee;"><p style="margin:0;color:#999999;font-size:12px;text-align:center;">Beckhoff Automation &mdash; Envoi automatique depuis TwinCAT 3</p></td></tr></table></td></tr></table></body></html>';
	nPriority     : UDINT; 
	R_Edge     : R_TRIG;
	bStart     : BOOL;
	bError     : BOOL;
	udErrId     : UDINT;
	nMailCounter : UDINT := 0;
	sFiles     : ARRAY [0..32] OF STRING(80) := ['C:\Temp\Temperatures.txt','C:\Temp\Log.txt']; (* TODO: Adjust file paths*)	
	bBusy: BOOL;
	nErrId: UDINT;
	nState: INT;


END_VAR 

fbSendMail(
	sNetId:= '', 
	sSmtpServer:= sSmtpServer, 
	sUsername:= sUsername, 
	sPassword:= sPassword, 
	nEncryption:= 1, 
	sFrom:= sFrom, 
	sTo:= sTo, 
	sCc:= , 
	sBcc:= , 
	sSubject:= 'Email from your Beckhoff PLC', 
	pMessage:= ADR(sMessage), 
	cbMessage:= SIZEOF(sMessage), 
	bExecute:= bStart, 
	tTimeout:= DEFAULT_ADS_TIMEOUT, 
	bBusy=> bBusy, 
	bError=> bError, 
	nErrId=> nErrId);	
	
IF NOT bBusy  AND bStart THEN
bStart := FALSE;
END_IF 


fbSmtpFull(
	sNetId:= '', 
	sSmtpServer:= sSmtpServer, 
	sUsername:= sUsername, 
	sPassword:= sPassword, 
	nEncryption:= 1, 
	sFrom:= sFrom, 
	sTo:= sTo, 
	sCc:= , 
	sBcc:= , 
	sDispositionNotification:= , 
	sReturnReceipt:= , 
	nPriority:= 1, 
	nSensitivity:= , 
	nPort:= , 
	nContentType:= 2, 
	sSubject:= 'Email from your Beckhoff PLC SMPTFull', 
	pMessage:= ADR(sMessage_HTML), 
	cbMessage:= SIZEOF(sMessage_HTML), 
	sAttachments:= sFiles, 
	bExecute:= bStart, 
	tTimeout:=DEFAULT_ADS_TIMEOUT , 
	bBusy=> bBusy, 
	bError=> bError, 
	nErrId=>nErrId );
	
IF NOT bBusy  AND bStart THEN
	nState := 0;
	bStart := FALSE;
END_IF 

```


# Gestion des accents dans HTML

Il faut ajouter la déclaration d'encodage **UTF-8** dans le `<head>` du HTML :

html

```html
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
```

Voici le HTML complet sur une ligne avec les accents et l'encodage UTF-8 :

html

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"><html><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8"><title>Alerte PLC</title></head><body style="margin:0;padding:0;background-color:#f4f4f4;font-family:Arial,sans-serif;"><table width="100%" cellpadding="0" cellspacing="0" style="background-color:#f4f4f4;padding:30px 0;"><tr><td align="center"><table width="600" cellpadding="0" cellspacing="0" style="background-color:#ffffff;border-radius:8px;overflow:hidden;box-shadow:0 2px 8px rgba(0,0,0,0.1);"><tr><td style="background-color:#006DB7;padding:24px 32px;"><h1 style="margin:0;color:#ffffff;font-size:20px;">&#9881; Beckhoff PLC Notification</h1></td></tr><tr><td style="padding:32px;"><h2 style="color:#333333;font-size:18px;margin-top:0;">Bonjour,</h2><p style="color:#555555;font-size:15px;line-height:1.6;">Cet e-mail a été envoyé automatiquement depuis votre automate Beckhoff.</p><table width="100%" cellpadding="0" cellspacing="0" style="background-color:#f0f7ff;border-left:4px solid #006DB7;border-radius:4px;margin:24px 0;"><tr><td style="padding:16px;"><p style="margin:0;color:#333;font-size:14px;"><b>Machine :</b> PLC-01</p><p style="margin:8px 0 0;color:#333;font-size:14px;"><b>Statut :</b> <span style="color:#28a745;">&#10003; Opérationnel</span></p><p style="margin:8px 0 0;color:#333;font-size:14px;"><b>Heure :</b> 14:32:05</p></td></tr></table><p style="color:#555555;font-size:15px;line-height:1.6;">Veuillez ne pas répondre à cet e-mail, il est envoyé automatiquement.</p></td></tr><tr><td style="background-color:#f4f4f4;padding:16px 32px;border-top:1px solid #eeeeee;"><p style="margin:0;color:#999999;font-size:12px;text-align:center;">Beckhoff Automation &mdash; Envoi automatique depuis TwinCAT 3</p></td></tr></table></td></tr></table></body></html>
```

---

⚠️ **Attention cependant** — même avec le `charset=UTF-8`, TwinCAT peut poser problème car les variables `STRING` en TwinCAT sont encodées en **ASCII/Latin-1** par défaut, pas en UTF-8. Si les accents s'affichent toujours mal, la solution la plus fiable reste d'utiliser les **entités HTML** :

|Caractère|Entité HTML|
|---|---|
|é|`&eacute;`|
|è|`&egrave;`|
|ê|`&ecirc;`|
|à|`&agrave;`|
|ù|`&ugrave;`|
|ô|`&ocirc;`|

Exemple :

```
Opérationnel → Op&eacute;rationnel
envoyé → envoy&eacute;
```

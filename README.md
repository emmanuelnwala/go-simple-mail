The best way to send emails in Go with SMTP Keep Alive and Timeout for Connect and Send.

**Download**

```bash
go get -u github.com/joegrasse/mail
```

**Usage**

```go
package main

import (
	"github.com/joegrasse/mail"
	"log"
)

func main() {

	htmlBody :=
`<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
		<title>Hello Gophers!</title>
	</head>
	<body>
		<p>This is the <b>Go gopher</b>.</p>
		<p><img src="cid:Gopher.png" alt="Go gopher" /></p>
		<p>Image created by Renee French</p>
	</body>
</html>`

	server := mail.NewSMTPServer()
	
	//SMTP Server
	server.Host = "smtp.example.com"
	server.Port = 587
	server.Username = "test@example.com"
	server.Password = "examplepass"
	server.Encryption = mail.EncryptionTLS
	
	//Variable to keep alive connection
	server.KeepAlive = true
	
	//Timeout for connect to SMTP Server
	server.ConnectTimeout = 10
	
	//Timeout for send the data and wait respond
	server.SendTimeout = 10
	
	//SMTP client
	smtpServer,err :=server.Connect()
	
	if err != nil{
		log.Fatal(err)
	}

	//New email simple html with inline and CC
	email := mail.New()

	email.SetFrom("From Example <nube@example.com>").
		AddTo("xhit@example.com").
		AddCc("otherto@example.com").
		SetSubject("New Go Email")

	email.SetBody("text/html", htmlBody)

	email.AddInline("/path/to/image.png", "Gopher.png")

	err = email.Send(smtpServer,server)

	if err != nil {
		log.Println(err)
	} else {
		log.Println("Email Sent")
	}


	//Other email with same connection and attachments
	email = mail.New()
	
	email.SetFrom("HELLO <nube@example.com>").
	AddTo("xhit@example.com").
		SetSubject("dfgdfgdf")

	email.SetBody("text/plain", "Hello Gophers!")
	email.AddAlternative("text/html", htmlBody)

	email.AddAttachment("path/to/file","filename test")
	email.AddAttachment("path/to/file2")

	err = email.Send(smtpServer,server)

	if err != nil {
		log.Println(err)
	} else {
		log.Println("Email Sent")
	}
}
```
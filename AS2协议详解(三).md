# AS2协议详解(三)

原创2022-05-11 09:23·[隐形喷火龙](https://www.toutiao.com/c/user/token/MS4wLjABAAAA41xegbr1tKpo0qS3crUgIi9ZIvBKbftndUGNBeZS2tw/?source=tuwen_detail)

AS2协议本身比较复杂，我们不需要了解其中太多细节，只需要知道一些重要概念就行了。

AS2是基于HTTP/HTTPS的，消息的格式使用MIME,就是邮件的格式，使用SHA1或SHA2加RSA进行签名，使用S/MIME进行加密。S/MIME加密本质就是3DES或者AES加RSA加密，有了之前的基础应该就很清楚了。

AS2的通信双方称为partner,每个partner有个partnerId,通信双方需要交互彼此的公钥。

自己实现S/MIME加密比较复杂，我们引入以下包来加密，jdk15on支持jdk1.5-jdk1.8,其他jdk版可去maven上搜索对应的版本

```
    <dependency>
        <groupId>org.bouncycastle</groupId>
        <artifactId>bcmail-jdk15on</artifactId>
        <version>1.70</version>
    </dependency>
```

![AS2协议详解(三)](https://p26.toutiaoimg.com/origin/tos-cn-i-qvj2lq49k0/a37d1bcf212748109f9ee3307f300a3b?from=pc)

接下来是实现AS2发送文件的JAVA版本

```
@Test
public void testAS2() throws Exception {
    //注册证书提供者BC
    Security.addProvider(new org.bouncycastle.jce.provider.BouncyCastleProvider());
    String password = "testas2";
    //生成mime消息体，放入待发送文件
    MimeBodyPart finalMessage = new MimeBodyPart();
    File file = new File("D:\\workspace\\data\\a.xml");
    finalMessage.setDataHandler(new DataHandler(new FileDataSource(file)));
    finalMessage.setHeader("Content-Type", "application/xml");
    finalMessage.setHeader("Content-Transfer-Encoding", "base64");
    finalMessage.setFileName(file.getName());
    //加载证书
    FileInputStream fis = new FileInputStream(new File("D:\\workspace\\OpenAs2App\\Server\\src\\config\\as2_certs.p12"));
    KeyStore keyStore = getKeyStore(fis, password, "PKCS12", "BC");
    PrivateKey privateKey = getPrivateKeyFromKeyStore(keyStore, "mycompany", "password");
    //签名
    X509Certificate signCert = getCertificate(keyStore, "mycompany");
    List certList = new ArrayList();
    certList.add(signCert);
    Store certs = new JcaCertStore(certList);

    SMIMESignedGenerator signer = new SMIMESignedGenerator();
    signer.setContentTransferEncoding("base64");
    //使用SHA1进行签名
    signer.addSignerInfoGenerator(new JcaSimpleSignerInfoGeneratorBuilder().setProvider("BC")
            .build("SHA1WITHRSA", privateKey, signCert));
    signer.addCertificates(certs);
    MimeMultipart signedMimeMultipart = signer.generate(finalMessage);
    finalMessage = new MimeBodyPart();
    finalMessage.setContent(signedMimeMultipart);
    finalMessage.setHeader("Content-Type", signedMimeMultipart.getContentType());

    //加密
    // 加载partner的数字证书
    X509Certificate cert = getCertificate(keyStore, "partnera");
    // 创建加密器
    SMIMEEnvelopedGenerator encryptor = new SMIMEEnvelopedGenerator();
    encryptor.addRecipientInfoGenerator(new JceKeyTransRecipientInfoGenerator(cert).setProvider("BC"));
    encryptor.setContentTransferEncoding("base64");
    //3DES加密
    JceCMSContentEncryptorBuilder jceCMSContentEncryptorBuilder =
            new JceCMSContentEncryptorBuilder(new ASN1ObjectIdentifier(SMIMEEnvelopedGenerator.DES_EDE3_CBC)).setProvider("BC");
    jceCMSContentEncryptorBuilder.setSecureRandom(new SecureRandom());
    // 进行加密
    MimeBodyPart encryptedPart = encryptor.generate(finalMessage, jceCMSContentEncryptorBuilder.build());

    //准备头字段
    InternetHeaders ih = new InternetHeaders();
    ih.addHeader("Connection", "close, TE");
    ih.addHeader("Message-ID", UUID.randomUUID().toString());
    ih.addHeader("Mime-Version", "1.0");
    ih.addHeader("Content-Type", encryptedPart.getContentType());
    ih.addHeader("AS2-To", "PartnerA_OID");
    ih.addHeader("AS2-From", "MyCompany_OID");
    ih.addHeader("Subject", "Subject: File a.xml sent from MyCompany to PartnerA");
    ih.addHeader("Disposition-Notification-To", "edi@myCompany.com");
    ih.addHeader("Content-Disposition", "attachment");

    String url = "http://localhost:10080";
    execRequest("POST", url, ih.getAllHeaders(), null, encryptedPart.getInputStream());
}

private static X509Certificate getCertificate(KeyStore keyStore,                                              String alias) throws Exception {
    Certificate certificate = keyStore.getCertificate(alias);
    return (X509Certificate) certificate;
}
```

![AS2协议详解(三)](https://p26.toutiaoimg.com/origin/tos-cn-i-qvj2lq49k0/ab2189a4d1f541bdb5d200a1eca9da57?from=pc)

发送http请求的代码

```
public static void execRequest(String method, String url, Enumeration<Header> headers, NameValuePair[] params, InputStream inputStream) throws Exception {
    HttpClientBuilder httpBuilder = HttpClientBuilder.create();
    URL urlObj = new URL(url);
    /*     * httpClient is used for this request only,     * set a connection manager that manages just one connection.     */
    if (urlObj.getProtocol().equalsIgnoreCase("https")) {
        /*         * Note: registration of a custom SSLSocketFactory via httpBuilder.setSSLSocketFactory is ignored when a connection manager is set.         * The custom SSLSocketFactory needs to be registered together with the connection manager.         */
        SSLConnectionSocketFactory sslCsf = buildSslFactory();
        httpBuilder.setConnectionManager(new BasicHttpClientConnectionManager(RegistryBuilder.<ConnectionSocketFactory>create().register("http", PlainConnectionSocketFactory.getSocketFactory()).register("https", sslCsf).build()));
    } else {
        httpBuilder.setConnectionManager(new BasicHttpClientConnectionManager());
    }
    RequestBuilder rb = getRequestBuilder(method, urlObj, params, headers);

    if (inputStream != null) {
        InputStreamEntity ise = new InputStreamEntity(inputStream);
        rb.setEntity(ise);
    }
    final HttpUriRequest request = rb.build();

    try (CloseableHttpClient httpClient = httpBuilder.build()) {
        try (CloseableHttpResponse response = httpClient.execute(request)) {
            HttpEntity entity = response.getEntity();
            String result = EntityUtils.toString(entity);
            System.out.println(result);
        }
    }
}

private static SSLConnectionSocketFactory buildSslFactory() throws Exception {
    boolean overrideSslChecks = true;
    SSLContext sslcontext;
    sslcontext = SSLContexts.createSystemDefault();
    // String [] protocols = Properties.getProperty(HTTP_PROP_SSL_PROTOCOLS,
    // "TLSv1").split("\\s*,\\s*");
    HostnameVerifier hnv = SSLConnectionSocketFactory.getDefaultHostnameVerifier();
    if (overrideSslChecks) {
        hnv = new HostnameVerifier() {
            @Override
            public boolean verify(String hostname, SSLSession session) {
                return true;
            }
        };
    }
    SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslcontext, null, null, hnv);
    return sslsf;
}

private static RequestBuilder getRequestBuilder(String method, URL urlObj, NameValuePair[] params, Enumeration<Header> headers) throws URISyntaxException {
    RequestBuilder req = null;
    if (method == null || method.equalsIgnoreCase(Method.GET)) {
        //default get
        req = RequestBuilder.get();
    } else if (method.equalsIgnoreCase(Method.POST)) {
        req = RequestBuilder.post();
    } else if (method.equalsIgnoreCase(Method.HEAD)) {
        req = RequestBuilder.head();
    } else if (method.equalsIgnoreCase(Method.PUT)) {
        req = RequestBuilder.put();
    } else if (method.equalsIgnoreCase(Method.DELETE)) {
        req = RequestBuilder.delete();
    } else if (method.equalsIgnoreCase(Method.TRACE)) {
        req = RequestBuilder.trace();
    } else {
        throw new IllegalArgumentException("Illegal HTTP Method: " + method);
    }
    req.setUri(urlObj.toURI());
    if (params != null && params.length > 0) {
        req.addParameters(params);
    }
    if (headers != null) {
        while (headers.hasMoreElements()) {
            Header header = headers.nextElement();
            String headerValue = header.getValue();
            req.setHeader(header.getName(), headerValue);
        }
    }
    return req;
}

public abstract static class Method {
    public static final String GET = "GET";
    public static final String HEAD = "HEAD";
    public static final String POST = "POST";
    public static final String PUT = "PUT";
    public static final String DELETE = "DELETE";
    public static final String TRACE = "TRACE";
    public static final String CONNECT = "CONNECT";
}
```

![AS2协议详解(三)](https://p26.toutiaoimg.com/origin/tos-cn-i-qvj2lq49k0/01984d45d3e542a48451c3bd31c960ec?from=pc)

代码讲解

来看这段代码，这段代码是加载证书库，方法上篇给出了，类型是PKCS12,BC是什么鬼？BC是证书提供者，如果是自己生成的证书，提供者是SUN，就不需要传了，我们这个证书是BC提供的所以需要传。

```
KeyStore keyStore = getKeyStore(fis, password, "PKCS12", "BC");
```

![AS2协议详解(三)](https://p26.toutiaoimg.com/origin/tos-cn-i-qvj2lq49k0/73ede39e310a48a3815a9c34480d89ae?from=pc)

生成mime消息体，放入待发送文件那段代码很简单，就是获取待发送文件的输入流然后生成mime消息体，没啥好讲的,加载证书库获取私钥的代码前篇都讲过了，也很简单。

接下来是签名和加密，都是固定写法，唯一有改动的可能是签名的算法是SHA1还是SHA2，加密算法是3DES还是AES，需要告知接收方。这边需要注意的是签名是使用自己的私钥，对方用我们的公钥进行验签，加密是使用对方的公钥，对方用自己的私钥进行解密。

接下去就是准备头字段，填写自己的partnerId和对方的partnerId就行了

其实有现成的代码，集成AS2不难，如果是自己去找协议去实现，难度就相当大了。
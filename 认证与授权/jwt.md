## 什么是Audience
虽然[官方文档](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.3)解释了什么是 `aud`，但是还是模棱两可。一开始以为是一个类似于 `app_id` 的字符串，例如“xxx blog app”。不过在 auth0 的[论坛](https://community.auth0.com/t/what-is-the-audience/71414)上看见，它应当是 URI 绝对路径，例如 `"aud": "https://test-api"`。这就更混乱了。

结合 [StackOverflow](https://stackoverflow.com/questions/28418360/jwt-json-web-token-audience-aud-versus-client-id-whats-the-difference) 以及 [Quora](https://www.quora.com/How-can-I-understand-audience-and-issuer-in-JWT-authentication) 上的提问，audience 的解释权应该归应用系统所有。也就是说，它到底是什么应该由你自己决定，它可以是任意的字符串。其具体含义是该令牌（token）的接收者是谁。

一个应用，一家公司，甚至是更高级的概念中，也许存在很多令牌，audience 指明了到底谁该接收这个令牌。例如，对 Google Drive 有效的令牌，GMail 不该接收它，甚至二者拥有同一签发者（当然你想要二者都接收的话只需要添加多个 audience 即可）。为什么要这么做？**因为一个用户可能注册了 GMail，但是没有注册 Google Drive，即使这两项产品都是 Google 公司的。**

从用户角度出发，某 Q 用户想要进行第三方（某讯）授权登录 xxx 小说网，yyy 音乐网以及 zzz 视频网。这三个网站属于不同的公司。这时第三方会为某 Q 用户签发三个 jwt，每个 jwt 中都应该填入对应的 audience。否则用户将可以使用任意一个 jwt 访问任意一个网站。这显然不合理。

你可能会想不是还有秘钥（secret）吗？不同网站的秘钥应该是不同的吧？确实可以用秘钥规避以上的问题，但是万一想要做更细粒度的控制呢？比如在同一个产品内，秘钥是相同的，但是还想要细划分出多个受众，这就很有用了。你可能又会想那同一个产品中的不同受众也设置不同的秘钥不就行了？这个可能真不行，因为加密的时候只能用一个秘钥。如果有多个秘钥，那么应该选哪个秘钥进行加密？这是个问题。
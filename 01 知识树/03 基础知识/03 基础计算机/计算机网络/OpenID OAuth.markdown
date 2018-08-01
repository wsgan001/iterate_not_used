# OpenID OAuth


TODO


* 到底什么是OpenID？为什么需要有OpenID？
* 什么是OpenAuth？


OpenID是Authentication
OAuth是Authorization

前者是网站对用户进行认证，让网站知道“你是你所声称的URL的属主”
后者其实并不包括认证，只不过“只有认证成功的人才能进行授权”，结果类似于“认证+授权”了。OAuth相当于：A网站给B网站一个令牌，然后告诉B网站说根据这个令牌你可以获取到某用户在A网站上允许你访问的所有信息

如果A网站需要用B网站的用户系统进行登录（学名好像叫federated login），它可以




  * 选择OpenID认证，然后通过attribute exchange获取用户的昵称或其他通过OpenID暴露出来的用户属性，或者


  * 选择OAuth认证，获取到token后再用token获取用户昵称或其他允许被访问的信息


关于OAuth的授权，不能说是滥用，是OAuth Service Provider对OAuth的权限没有细分。好比我只需要用户的昵称性别，你却把修改昵称性别的权限也授权给我了（虽然我不一定会去用）。这个错在OAuth Service Provider


作者：yegle
链接：https://www.zhihu.com/question/19628327/answer/12591409
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

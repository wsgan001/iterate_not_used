AIMS

已经遇到两次这个问题了，这次 npm install gitbook-cli -g 的时候又遇到了，因为Out of memory 。我看了下 /var/log/messages 的确是说 Out of memory 。

查了下，都说要创建交换区：



事实证明，创建这个交换区之后，再次运行 install 的确是可以的。



REF

* [NPM install - killed error solution](http://owenyang0.github.io/2015/02/09/NPM-install-killed-error-solution/)
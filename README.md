# snappy-erlang-nif

This is an Erlang NIF for Google's snappy fast compressor and decompressor.

Google snappy's official repository and bug tracking system is at:

* https://github.com/google/snappy

Its source is included in this project.

# site

https://github.com/fdmanana/snappy-erlang-nif

# performance tests

Snappy is much faster than zlib's deflate compression, specially for reasonably
large amounts of data. Here follow a few basic tests.

```erl
Erlang/OTP 26 [erts-14.2.5.10] [source] [64-bit] [smp:16:16] [ds:16:16:10] [async-threads:1]

Eshell V14.2.5.10 (press Ctrl+G to abort, type help(). for help)
1> code:add_path("ebin").
true
2> {ok, J} = file:read_file("doc_11k.json").
{ok,<<"{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1Rg0sekoUawsGZL"...>>}
3> timer:tc(snappy, compress, [J]).
{251616,
 {ok,<<"øUô÷*{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1R"...>>}}
4> timer:tc(snappy, compress, [J]).
{59,
 {ok,<<"øUô÷*{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1R"...>>}}
5> timer:tc(snappy, compress, [J]).
{36,
 {ok,<<"øUô÷*{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1R"...>>}}
6> timer:tc(snappy, compress, [J]).
{57,
 {ok,<<"øUô÷*{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1R"...>>}}
7> timer:tc(snappy, compress, [J]).
{34,
 {ok,<<"øUô÷*{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1R"...>>}}
8> timer:tc(snappy, compress, [J]).
{53,
 {ok,<<"øUô÷*{\n  \"data3\": \"vh3w3TyPAxe01SOJhOgTMzOI8vO6tUZv0h36XVdRvECcDBJW5VAeLOJC1u9NRumMg5BcDp932M41Plr1R"...>>}}
9>
   timer:tc(zlib, zip, [J]).
{461,
 <<21,154,71,14,165,74,18,69,231,127,21,95,181,2,188,233,
   25,222,123,207,12,239,125,98,91,189,247,...>>}
10> timer:tc(zlib, zip, [J]).
{431,
 <<21,154,71,14,165,74,18,69,231,127,21,95,181,2,188,233,
   25,222,123,207,12,239,125,98,91,189,247,...>>}
11> timer:tc(zlib, zip, [J]).
{440,
 <<21,154,71,14,165,74,18,69,231,127,21,95,181,2,188,233,
   25,222,123,207,12,239,125,98,91,189,247,...>>}
12> timer:tc(zlib, zip, [J]).
{332,
 <<21,154,71,14,165,74,18,69,231,127,21,95,181,2,188,233,
   25,222,123,207,12,239,125,98,91,189,247,...>>}
13> timer:tc(zlib, zip, [J]).
{303,
 <<21,154,71,14,165,74,18,69,231,127,21,95,181,2,188,233,
   25,222,123,207,12,239,125,98,91,189,247,...>>}
14> timer:tc(zlib, zip, [J]).
{406,
 <<21,154,71,14,165,74,18,69,231,127,21,95,181,2,188,233,
   25,222,123,207,12,239,125,98,91,189,247,...>>}
15>
    byte_size(element(2, snappy:compress(J))).
11005
16> byte_size(zlib:zip(J)).
8284
```

```erl
17> crypto:start().
ok
18> Large = base64:encode(crypto:strong_rand_bytes(100 * 1024)).
<<"E8CcPijHF6N2/WMTvRkLr7FdkCWlndHx8iVh3n7ZPHD3khPnzPefazjDk7Ox2B1zIxA2TVB+4RDBSk2DmDXQM4sasup06Y2uZrsWgnUF9DBTkJVxuB3L"...>>
19> timer:tc(snappy, compress, [Large]).
{201,
 {ok,<<"Øª\bôÿÿE8CcPijHF6N2/WMTvRkLr7FdkCWlndHx8iVh3n7ZPHD3khPnzPefazjDk7Ox2B1zIxA2TVB+4RDBSk2DmDXQM4sasup06Y"...>>}}
20> timer:tc(snappy, compress, [Large]).
{204,
 {ok,<<"Øª\bôÿÿE8CcPijHF6N2/WMTvRkLr7FdkCWlndHx8iVh3n7ZPHD3khPnzPefazjDk7Ox2B1zIxA2TVB+4RDBSk2DmDXQM4sasup06Y"...>>}}
21> timer:tc(snappy, compress, [Large]).
{179,
 {ok,<<"Øª\bôÿÿE8CcPijHF6N2/WMTvRkLr7FdkCWlndHx8iVh3n7ZPHD3khPnzPefazjDk7Ox2B1zIxA2TVB+4RDBSk2DmDXQM4sasup06Y"...>>}}
22> timer:tc(snappy, compress, [Large]).
{312,
 {ok,<<"Øª\bôÿÿE8CcPijHF6N2/WMTvRkLr7FdkCWlndHx8iVh3n7ZPHD3khPnzPefazjDk7Ox2B1zIxA2TVB+4RDBSk2DmDXQM4sasup06Y"...>>}}
23>
    timer:tc(zlib, zip, [Large]).
{7116,
 <<20,155,197,114,227,64,20,69,63,72,11,49,45,197,44,89,
   12,59,49,51,235,235,39,179,74,57,113,...>>}
24> timer:tc(zlib, zip, [Large]).
{7254,
 <<20,155,197,114,227,64,20,69,63,72,11,49,45,197,44,89,
   12,59,49,51,235,235,39,179,74,57,113,...>>}
25> timer:tc(zlib, zip, [Large]).
{7136,
 <<20,155,197,114,227,64,20,69,63,72,11,49,45,197,44,89,
   12,59,49,51,235,235,39,179,74,57,113,...>>}
26> timer:tc(zlib, zip, [Large]).
{6760,
 <<20,155,197,114,227,64,20,69,63,72,11,49,45,197,44,89,
   12,59,49,51,235,235,39,179,74,57,113,...>>}
27> timer:tc(zlib, zip, [Large]).
{7349,
 <<20,155,197,114,227,64,20,69,63,72,11,49,45,197,44,89,
   12,59,49,51,235,235,39,179,74,57,113,...>>}
28>
    byte_size(element(2, snappy:compress(Large))).
136548
29> byte_size(zlib:zip(Large)).
103438
```

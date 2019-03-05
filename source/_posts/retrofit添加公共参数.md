---
title: okhttp和retrofit网络框架的使用
date: 2018-07-17 01:27:03
tags:
- android
---
```
 OkHttpClient client= new OkHttpClient.Builder().addInterceptor(new Interceptor() {
                    @Override
                    public okhttp3.Response intercept(Chain chain) throws IOException {

                        //获取到request
                        Request request = chain.request();
                        HttpUrl.Builder builder=request.url().newBuilder().
                                scheme(request.url().scheme()).host(request.url().host());
                        //添加公共参数
                        builder.addQueryParameter("","");

                        request = request.newBuilder().method(request.method(),request.body()).url(builder.build()).build();
                        Tool.log(request.url().toString());
                        return chain.proceed(request);
                    }
                }).build();
                mRetrofit=new Retrofit.Builder().addConverterFactory(GsonConverterFactory.create()).
                       client(client).
                        baseUrl(BASE_URL).build();
```

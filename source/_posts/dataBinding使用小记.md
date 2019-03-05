---
title: dataBinding使用小记
date: 2019-02-28 10:02:58
tags:
- android
---
因为不可能一次性写完,遇到一点写一点
## 开启dataBinding

* 在app下的build.gradle中加入:
     dataBinding.enabled = true

2019-02-28:
* 实现双向绑定

下面是一个简易的dataBinding的Demo:


            User user=new User();
            ActivityMainBinding binding;
            boolean flag=false;
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                binding=DataBindingUtil.setContentView(this,R.layout.activity_main);

                user.age=23;
                user.sex="men";
                user.name="Tony";
                binding.setUser(user);

            }

            public void change(View view) {
                if(flag){
                    user.setName("Tony");
                    user.setSex("men");
                    user.setAge(23);
                }else {
                    user.setName("Kitty");
                    user.setSex("women");
                    user.setAge(20);
                }
                binding.setUser(user);
                flag=!flag;
            }

其中change是按钮的点击事件来更新页面上显示的参数,User是一个简单的javaBean

很典型的每次在数据切换时,我们都会调用binding.setUser(user)来设置新数据

重写User


        public class User extends BaseObservable {
            public String name;
            public int age;
            public String sex;
            @Bindable
            public String getName() {
                return name;
            }

            public void setName(String name) {
                this.name = name;
                notifyPropertyChanged(BR.name);
            }
            @Bindable
            public int getAge() {
                return age;
            }

            public void setAge(int age) {
                this.age = age;
                notifyPropertyChanged(BR.age);
            }
            @Bindable
            public String getSex() {
                return sex;
            }

            public void setSex(String sex) {
                this.sex = sex;
                notifyPropertyChanged(BR.sex);
            }
        }
在每个set方法里面加入了notifyPropertyChanged(),每个get()方法加上注解@Bindable  这样只要每次更新我们关心的数据就好了

上面是针对对象,如果只是某一个数值,那么可以使用ObservableField<T> 来 定义  然后再xml里面使用

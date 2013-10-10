---
author: gavin
comments: false
date: 2013-08-23 16:34:43
layout: post
slug: cocos2d-x%e5%ad%a6%e4%b9%a0%ef%bc%88%e4%ba%8c%ef%bc%89
title: cocos2d-x学习（二）
wordpress_id: 241
categories:
- Cocos2d-x
tags:
- cocos2d-x
---

## 基础知识





cocos2d-x引擎将一个游戏抽象成4个概念：导演（CCDirector）、场景（CCScene）、布景（CCLayer）和精灵（CCSprite）。下面简单介绍一下几种概念：







  * 导演：整个游戏的组织者


  * 场景：可以是一个关卡或者一个界面，多个场景组成了整个游戏。


  * 布景：一个场景可以有多个布景，比如游戏对象布景和地图布景。


  * 精灵：主角，NPC等等。





只要了解了这几个概念之间的关系，就可以看HelloWorld了。





## cocos2d-x项目代码结构





打开Classes文件夹，可以从文件名上看出整个项目的代码组织形式：







  * `AppDelegate.h`和`AppDelegate.cpp`声明和定义了最高层逻辑，即导演。


  * `HelloWorldScene.h`和`HelloWorldScene.cpp`声明和定义了场景内的逻辑，包括场景、布景和精灵。


  * `AppMacros.h`：定义了一些程序运行时的属性。





## 深入理解HelloCpp项目源码





以android工程为例，打开`proj.android/jni/hellocpp/main.cpp`，代码如下：




    
    <code>#include "AppDelegate.h"
    #include "platform/android/jni/JniHelper.h"
    #include <jni.h>
    #include <android/log.h>
    
    #include "cocos2d.h"
    
    #define  LOG_TAG    "main"
    #define  LOGD(...)  __android_log_print(ANDROID_LOG_DEBUG,LOG_TAG,__VA_ARGS__)
    
    using namespace cocos2d;
    
    void cocos_android_app_init (void) {
        LOGD("cocos_android_app_init");
        AppDelegate *pAppDelegate = new AppDelegate();
    }</code>





### AppDelegate.cpp





上面`cocos_android_app_init`方法中初始化了一个`AppDelegate`对象。顾名思义，这个对象代表的是应用，它实际上是cocos2d-x引擎的入口。进入`AppDelegate.h`:




    
    <code>#ifndef  _APP_DELEGATE_H_
    #define  _APP_DELEGATE_H_
      
    #include "cocos2d.h"
    /**
    @brief    The cocos2d Application.
    这里使用私有继承的原因是需要隐藏一些由Director调用的接口.
    */
    class  AppDelegate : private cocos2d::Application
    {
    public:
        AppDelegate();
        virtual ~AppDelegate();
    
        /**
        @brief    这里初始化Director和Scene.
        @return true    初始化成功，应用继续.
        @return false   初始化失败，应用终止.
        */
        virtual bool applicationDidFinishLaunching();
    
        /**
        @brief  应用进入后台时，调用此方法
        @param  the pointer of the application
        */
        virtual void applicationDidEnterBackground();
    
        /**
        @brief  应用进入前台时，调用此方法
        @param  the pointer of the application
        */
        virtual void applicationWillEnterForeground();
    
    };  
    #endif // _APP_DELEGATE_H_</code>





上面是AppDelegate类的声明，AppDelegate类的定义如下：




    
    <code>#include "AppDelegate.h"  
    #include <vector>
    #include <string>  
    #include "HelloWorldScene.h"
    #include "AppMacros.h"
    
    USING_NS_CC;
    using namespace std;
    
    AppDelegate::AppDelegate() {  
    }
    
    AppDelegate::~AppDelegate() 
    {
    }
    
    bool AppDelegate::applicationDidFinishLaunching() {
        // 初始化导演对象（director）
        auto director = Director::getInstance();
        auto glView = EGLView::getInstance();  
        director->setOpenGLView(glView);  
        auto size = director->getWinSize();  
        // 指定设计分辨率（design resolution），这里是默认值480*320
        glView->setDesignResolutionSize(designResolutionSize.width, designResolutionSize.height, ResolutionPolicy::NO_BORDER);  
        Size frameSize = glView->getFrameSize();  
        vector<string> searchPath;  
        // 在这个demo中，我们依照画面的高度来选择资源文件。
        // 如果资源尺寸与设计分辨率尺寸不同，就需要设置contentScaleFactor。
        // 我们使用资源高度与涉及分辨率高度的比例，这样可以保证资源的高度可以适配设计分辨率高度。  
        // 如果画面的高度大于中间资源的尺寸，则选择大尺寸资源。资源尺寸的定义在文件AppMacros.h中。
        if (frameSize.height > mediumResource.size.height)
        {
            searchPath.push_back(largeResource.directory);  
            director->setContentScaleFactor(MIN(largeResource.size.height/designResolutionSize.height, largeResource.size.width/designResolutionSize.width));
        }
        // 如果画面的高度大于小尺寸资源的尺寸，则选择中型资源。
        else if (frameSize.height > smallResource.size.height)
        {
            searchPath.push_back(mediumResource.directory);  
            director->setContentScaleFactor(MIN(mediumResource.size.height/designResolutionSize.height, mediumResource.size.width/designResolutionSize.width));
        }
        // 如果画面的高度比小尺寸资源的尺寸小，则选择小尺寸资源。
        else
        {
            searchPath.push_back(smallResource.directory);  
            director->setContentScaleFactor(MIN(smallResource.size.height/designResolutionSize.height, smallResource.size.width/designResolutionSize.width));
        }
    
        // 设置资源搜索路径
        FileUtils::getInstance()->setSearchPaths(searchPath);
    
        // 开启显示FPS
        director->setDisplayStats(true);
    
        // 设置FPS，如果不调用这个方法的话，默认值是1.0/60。
        director->setAnimationInterval(1.0 / 60);
    
        // 创建一个场景（scene）对象，它是一个能自动释放的对象。
        auto scene = HelloWorld::scene();
    
        // run
        director->runWithScene(scene);
    
        return true;
    }  
    // 当应用转到后台时会调用这个函数，比如有一个电话打进来时。
    void AppDelegate::applicationDidEnterBackground() {
        Director::getInstance()->stopAnimation();  
        // 如果使用SimpleAudioEngine的话，必须要暂停播放
        // SimpleAudioEngine::sharedEngine()->pauseBackgroundMusic();
    }  
    // 当应用再次进入活动状态时，调用这个函数
    void AppDelegate::applicationWillEnterForeground() {
        Director::getInstance()->startAnimation();  
        // 如果使用了SimpleAudioEngine，应该恢复播放
        // SimpleAudioEngine::sharedEngine()->resumeBackgroundMusic();
    }
    </code>





上面代码中最重要的一行是`auto scene = HelloWorld::scene();`，这行代码获得一个场景类对象，并交由导演对象管理此场景。





### HelloWorldScene.cpp





还是先看声明（HelloWorldScene.h）：




    
    <code>#ifndef __HELLOWORLD_SCENE_H__
    #define __HELLOWORLD_SCENE_H__
    
    #include "cocos2d.h"  
    class HelloWorld : public cocos2d::Layer
    {
    public:
        // 这里与cocos2d-iphone有所不同，cocos2d-x中的init方法返回值类型为bool，而cocos2d-iphone中返回id。
        virtual bool init();  
        // 由于cpp中没有id，因此建议返回类实例的指针。
        static cocos2d::Scene* scene();  
        // a selector callback
        void menuCloseCallback(Object* sender);  
        // implement the "static node()" method manually
        CREATE_FUNC(HelloWorld);
    };
    
    #endif // __HELLOWORLD_SCENE_H__
    </code>





`init`方法覆盖了基类`CCLayer`的方法，它会在`create()`方法中被调用：




    
    <code>CCLayer *CCLayer::create()
    {
        CCLayer *pRet = new CCLayer();
        if (pRet && pRet->init())
        {
            pRet->autorelease();
            return pRet;
        }
        else
        {
            CC_SAFE_DELETE(pRet);
            return NULL;
        }
    }</code>





Ok，继续看`HelloWorldScene.cpp`，代码如下：




    
    <code>#include "HelloWorldScene.h"
    #include "AppMacros.h"
    USING_NS_CC;
    
    CCScene* HelloWorld::scene()
    {
        // 'scene' is an autorelease object
        CCScene *scene = CCScene::create();
        
        // 'layer' is an autorelease object。这个方法调用了下面的init()，详情见上面的create方法代码。
        HelloWorld *layer = HelloWorld::create();
    
        // add layer as a child to scene，一个场景可以包含多个布景层。
        scene->addChild(layer);
    
        // return the scene
        return scene;
    }
    
    // "init"方法用于初始化类实例
    bool HelloWorld::init()
    {
        //////////////////////////////
        // 1. 首先调用父类的init方法，父类的init方法主要确定是否初始化了导演类，是则为其设置窗口尺寸，否则返回false。
        if ( !CCLayer::init() )
        {
            return false;
        }
        
        CCSize visibleSize = CCDirector::sharedDirector()->getVisibleSize();
        CCPoint origin = CCDirector::sharedDirector()->getVisibleOrigin();
    
        /////////////////////////////
        // 2. 添加一个退出程序的菜单
    
        // 首先，获得一个表示退出的图标，它是一个autorelease对象
        CCMenuItemImage *pCloseItem = CCMenuItemImage::create(
                                            "CloseNormal.png",
                                            "CloseSelected.png",
                                            this,
                                            menu_selector(HelloWorld::menuCloseCallback));
        
        pCloseItem->setPosition(ccp(origin.x + visibleSize.width - pCloseItem->getContentSize().width/2 ,
                                    origin.y + pCloseItem->getContentSize().height/2));
    
        // 创建一个菜单，它也是一个autorelease对象
        CCMenu* pMenu = CCMenu::create(pCloseItem, NULL);
        pMenu->setPosition(CCPointZero);
        this->addChild(pMenu, 1);
    
        /////////////////////////////
        // 3. add your codes below...
    
        // add a label shows "Hello World"
        // create and initialize a label
        CCLabelTTF* pLabel = CCLabelTTF::create("Hello World", "Arial", TITLE_FONT_SIZE);
        
        // position the label on the center of the screen
        pLabel->setPosition(ccp(origin.x + visibleSize.width/2,
                                origin.y + visibleSize.height - pLabel->getContentSize().height));
    
        // add the label as a child to this layer
        this->addChild(pLabel, 1);
    
        // add "HelloWorld" splash screen"
        CCSprite* pSprite = CCSprite::create("HelloWorld.png");
    
        // position the sprite on the center of the screen
        pSprite->setPosition(ccp(visibleSize.width/2 + origin.x, visibleSize.height/2 + origin.y));
    
        // add the sprite as a child to this layer
        this->addChild(pSprite, 0);
        
        return true;
    }
    
    
    void HelloWorld::menuCloseCallback(CCObject* pSender)
    {
        CCDirector::sharedDirector()->end();
    
    #if (CC_TARGET_PLATFORM == CC_PLATFORM_IOS)
        exit(0);
    #endif
    }
    </code>




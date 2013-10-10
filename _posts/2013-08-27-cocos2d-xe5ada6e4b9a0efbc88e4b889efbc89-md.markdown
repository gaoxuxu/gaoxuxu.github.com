---
author: gavin
comments: false
date: 2013-08-27 22:03:09
layout: post
slug: cocos2d-x%e5%ad%a6%e4%b9%a0%ef%bc%88%e4%b8%89%ef%bc%89-md
title: Cocos2d-x学习（三）
wordpress_id: 244
categories:
- Cocos2d-x
---

## TestCpp源码框架





TestCpp项目是专门用来演示cocos2d-x引擎各种特性的示例项目，因此它包含许多场景。此项目的源码框架主要是：







  * `AppDelegate.h & AppDelegate.cpp`：代表cocos2d-x应用。  

`class  AppDelegate : private cocos2d::CCApplication`


  * `controller.h & controller.cpp`：声明&定义了游戏的主界面，用来显示所有示例的列表。  

`class TestController : public CCLayer`


  * `testBasic.h & testBasic.cpp`：声明&定义了所有示例的基类`TestScene`，此类主要用来在示例中返回到主界面和示例间的前后跳转。  

`class TestScene : public CCScene`


  * `testResource.h`：定义了一些资源路径。


  * `tests.h`：包含了所有示例模块的头文件，还定义了一个表示示例ID的匿名枚举与所有示例名的`string`数组。


  * `VisibleRect.h & VisibleRect.cpp`：声明&定义了表示可视区域的矩形。


  * 剩余的都是演示各种cocos2d-x特性的示例。





## 基础模块的代码组织





看了源码框架，基础部分的组织感觉还是比较简单的，首先肯定是控制游戏启动、转到后台以及转入前台的`AppDelegate.h & AppDelegate.cpp`：





### AppDelegate





`AppDelegate.h`主要代码如下：




    
    <code>class  AppDelegate : private cocos2d::CCApplication
    {
    public:
        AppDelegate();
        virtual ~AppDelegate();
    
        virtual bool applicationDidFinishLaunching();
    
        virtual void applicationDidEnterBackground();
    
        virtual void applicationWillEnterForeground();
    };
    </code>





三个重写父类的虚函数中，`applicationDidFinishLaunching()`是程序的入口，可以在这里初始化导演类（CCDirector）和第一个场景类（CCScene）。`applicationDidEnterBackground()`是程序转入后台时的回调函数，可以在这里暂停背景音乐，甚至保存数据。`applicationWillEnterForeground()`是程序从后台转入前台时的回调函数，可以在这里恢复背景音乐和数据。  

以上算是总结了一下基础知识，`AppDelegate`类的具体实现如下（`AppDelegate.cpp`）：




    
    <code>AppDelegate::AppDelegate()
    {
    }
    
    AppDelegate::~AppDelegate()
    {
    //    SimpleAudioEngine::end();
        cocos2d::extension::CCArmatureDataManager::purgeArmatureSystem();//清空精灵缓存，释放引用。
    }
    
    bool AppDelegate::applicationDidFinishLaunching()
    {
        // As an example, load config file
        // XXX: This should be loaded before the Director is initialized,
        // XXX: but at this point, the director is already initialized
        CCConfiguration::sharedConfiguration()->loadConfigFile("configs/config-example.plist");
    
        // initialize director
        CCDirector *pDirector = CCDirector::sharedDirector();
        pDirector->setOpenGLView(CCEGLView::sharedOpenGLView());
    
        CCSize screenSize = CCEGLView::sharedOpenGLView()->getFrameSize();
    
        CCSize designSize = CCSizeMake(480, 320);
    
        CCFileUtils* pFileUtils = CCFileUtils::sharedFileUtils();
    
        if (screenSize.height > 320)
        {
            CCSize resourceSize = CCSizeMake(960, 640);
            std::vector<std::string> searchPaths;
            searchPaths.push_back("hd");
            pFileUtils->setSearchPaths(searchPaths);
            pDirector->setContentScaleFactor(resourceSize.height/designSize.height);
        }
    
        CCEGLView::sharedOpenGLView()->setDesignResolutionSize(designSize.width, designSize.height, kResolutionNoBorder);
    
        CCScene * pScene = CCScene::create();
        CCLayer * pLayer = new TestController();
        pLayer->autorelease();
    
        pScene->addChild(pLayer);
        pDirector->runWithScene(pScene);
    
        return true;
    }
    
    // This function will be called when the app is inactive. When comes a phone call,it's be invoked too
    void AppDelegate::applicationDidEnterBackground()
    {
        CCDirector::sharedDirector()->stopAnimation();
        SimpleAudioEngine::sharedEngine()->pauseBackgroundMusic();
        SimpleAudioEngine::sharedEngine()->pauseAllEffects();
    }
    
    // this function will be called when the app is active again
    void AppDelegate::applicationWillEnterForeground()
    {
        CCDirector::sharedDirector()->startAnimation();
        SimpleAudioEngine::sharedEngine()->resumeBackgroundMusic();
        SimpleAudioEngine::sharedEngine()->resumeAllEffects();
    }
    </code>





由以上的代码可以看出，`AppDelegate`类主要做以下几件事情：







  * 程序启动时初始化`CCDirector`，并根据屏幕尺寸为其设置资源查找路径和缩放因子。这个方法的最后是为主界面加载一个场景。


  * 程序进入后台时进行游戏的暂停工作。


  * 程序再次进入前台时进行游戏的恢复工作。





需要记住的两个知识点：







  * `CCFileUtils::sharedFileUtils()->setSearchPaths()`需要的参数是一个`vector`。


  * `CCEGLView::sharedOpenGLView()->setDesignResolutionSize(designSize.width, designSize.height, kResolutionNoBorder)`，`kResolutionNoBorder`表示屏幕尺寸与设计尺寸不同时，没有黑边，且画面会被裁剪。另外两个值分别是`kResolutionExactFit`和`kResolutionShowAll`，前者会拉伸画面，而后者将显示黑边。





### TestController





类`TestController`继承自`CCLayer`，其构造方法中就添加了两个`CCMenu`。一个是退出按钮，另一个是一列`CCMenuItem`。  

`TestController`类中比较有意思的是`void TestController::ccTouchesMoved(CCSet *pTouches, CCEvent *pEvent)`方法，此方法代码如下：




    
    <code>void TestController::ccTouchesMoved(CCSet *pTouches, CCEvent *pEvent)
    {
        CCSetIterator it = pTouches->begin();
        CCTouch* touch = (CCTouch*)(*it);
    
        CCPoint touchLocation = touch->getLocation();    
        float nMoveY = touchLocation.y - m_tBeginPos.y;
    
        CCPoint curPos  = m_pItemMenu->getPosition();
        CCPoint nextPos = ccp(curPos.x, curPos.y + nMoveY);
    
        if (nextPos.y < 0.0f)
        {
            m_pItemMenu->setPosition(CCPointZero);
            return;
        }
    
        if (nextPos.y > ((TESTS_COUNT + 1)* LINE_SPACE - VisibleRect::getVisibleRect().size.height))
        {
            m_pItemMenu->setPosition(ccp(0, ((TESTS_COUNT + 1)* LINE_SPACE - VisibleRect::getVisibleRect().size.height)));
            return;
        }
    
        m_pItemMenu->setPosition(nextPos);
        m_tBeginPos = touchLocation;
        s_tCurPos   = nextPos;
    }
    </code>





其逻辑很简单，就是根据触摸移动的距离重绘`CCMenuItem`，但是提供了一种在cocos2d-x中实现触摸滚动的思路。  

接下来，看每一项`CCMenuItem`的点击事件：




    
    <code>void TestController::menuCallback(CCObject * pSender)
    {
        // pSender就是被点击的项
        CCMenuItem* pMenuItem = (CCMenuItem *)(pSender);
        int nIdx = pMenuItem->getZOrder() - 10000;
    
        // 这里创建并运行要测试的场景
        TestScene* pScene = CreateTestScene(nIdx);
        if (pScene)
        {
            pScene->runThisTest();
            pScene->release();
        }
    }
    </code>





### TestScene





上一个代码块中的`TestScen`类其实现见`testBasic.cpp`。它是每一个将要测试的场景的基类，它主要是提供了一个从测试场景返回主界面的菜单：




    
    <code>void TestScene::onEnter()
    {
        //这里必须要调用基类的方法
        CCScene::onEnter();
    
        //add the menu item for back to main menu
        CCLabelTTF* label = CCLabelTTF::create("MainMenu", "Arial", 20);
        CCMenuItemLabel* pMenuItem = CCMenuItemLabel::create(label, this, menu_selector(TestScene::MainMenuCallback));
    
        CCMenu* pMenu =CCMenu::create(pMenuItem, NULL);
    
        pMenu->setPosition( CCPointZero );
        pMenuItem->setPosition( ccp( VisibleRect::right().x - 50, VisibleRect::bottom().y + 25) );
    
        addChild(pMenu, 1);
    }
    </code>




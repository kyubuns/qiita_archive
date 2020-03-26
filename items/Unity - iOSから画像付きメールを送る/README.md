# まえがき

「今時メール？？」って思うんですけど、なんだかんだメールが便利な状況はあります。
iOSから画像つきメールを送るコードを書いたので共有します。
うちでは簡易バグレポーターとして、Debug.LogExceptionやDebug.Assertを検出したときにスクリーンショットを撮影し、StackTraceと共にメールで送るということをしています。
挙動確認は、Mac上のUnityEditorおよびiOSでのみ行いました。

# 導入方法

以下の2つのファイルをUnityプロジェクトにつっこみます。

```UniMail.cs
using UnityEngine;
using System;
#if UNITY_IOS && !UNITY_EDITOR
using System.Collections;
using System.Runtime.InteropServices;
#endif

namespace UniMail
{
    public static class Mail
    {
        #if UNITY_IOS && !UNITY_EDITOR
        [DllImport("__Internal")]
        private static extern void _SendWithImage(string mailTo, string subject, string body, string imagePath);
        #endif

        public static void SendWithImage(string mailTo, string subject, string body, string imagePath)
        {
            #if UNITY_IOS && !UNITY_EDITOR
            _SendWithImage(mailTo, subject, body, imagePath);
            #else
            Application.OpenURL("mailto:" + mailTo + "?subject=" + Uri.EscapeDataString(subject) + "&body=" + Uri.EscapeDataString(body) + "&attachment=" + Uri.EscapeDataString(imagePath));
            #endif
        }
    }
}
```

```unimail.mm
#import "UnityAppController.h"
#import "UnityAppController+ViewHandling.h"
#import "UnityAppController+Rendering.h"
#import <MessageUI/MessageUI.h>
#import <MessageUI/MFMailComposeViewController.h>

@interface MailPlugin : UIViewController <MFMailComposeViewControllerDelegate>{}

-(void)sendMailWithImage:(NSString*)to Subject:(NSString*)subject Body:(NSString *)body ImagePath:(NSString *)imagePath;

-(void) mailComposeController:(MFMailComposeViewController *)controller didFinishWithResult:(MFMailComposeResult)result error:(NSError *)error;

@end

@implementation MailPlugin
-(void) sendMailWithImage:(NSString*)to Subject:(NSString*)subject Body:(NSString *)body ImagePath:(NSString *)imagePath
{
    if (![MFMailComposeViewController canSendMail]) return;
    
    MFMailComposeViewController *mailViewController = [[MFMailComposeViewController alloc] init];
    mailViewController.mailComposeDelegate = self;
    
    // subject
    [mailViewController setSubject:subject];
    
    // mail to
    NSArray *toRecipients = [NSArray arrayWithObject:to];
    [mailViewController setToRecipients:toRecipients];
    
    // body
    [mailViewController setMessageBody:body isHTML:NO];
    
    // image
    NSLog(@"image path:%@", imagePath);
    UIImage *image= [[UIImage alloc] initWithContentsOfFile:imagePath];
    NSData *data  = [[NSData alloc] initWithData:UIImagePNGRepresentation(image)];
    [mailViewController addAttachmentData:data mimeType:@"image/png" fileName:@"screen_shot.png"];
    
    [GetAppController().rootViewController presentViewController: mailViewController animated:YES completion:NULL];
}

-(void) mailComposeController:(MFMailComposeViewController *)controller didFinishWithResult:(MFMailComposeResult)result error:(NSError *)error{
    [GetAppController().rootViewController dismissViewControllerAnimated:YES completion:NULL];
}
@end

NSString* Mail_CreateNSString(const char* string)
{
    return [NSString stringWithUTF8String: string ? string : ""];
}

extern UIViewController* UnityGetGLViewController();
static MailPlugin *mailPlugin = NULL;

extern "C"
{
    void _SendWithImage(const char* charMailTo, const char* charSubject, const char* charBody, const char* charImagePath)
    {
        NSString *mailTo = Mail_CreateNSString(charMailTo);
        NSString *subject = Mail_CreateNSString(charSubject);
        NSString *body = Mail_CreateNSString(charBody);
        NSString *imagePath = Mail_CreateNSString(charImagePath);
        
        if(mailPlugin == NULL) mailPlugin = [[MailPlugin alloc] init];
        
        [mailPlugin sendMailWithImage:mailTo Subject:subject Body:body ImagePath:imagePath];
    }
}
```

unimail.mmは、以下のようにiOS pluginとして認識されるようにチェックをいれてください。

![Monosnap 2016-11-04 19-08-55.png](https://qiita-image-store.s3.amazonaws.com/0/6459/dafc0d9e-8191-9f85-0800-1349b00918ac.png)

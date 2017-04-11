
**Objetivo-C**:

1. No **QSAppDelegate.m**, importe do iOS SDK e **QSTodoService.h**:
        
        #import <MicrosoftAzureMobile/MicrosoftAzureMobile.h>
        #import "QSTodoService.h"

2. No `didFinishLaunchingWithOptions` na **QSAppDelegate.m**, inserir à direita de linhas seguintes antes `return YES;`:

        UIUserNotificationSettings* notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:notificationSettings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

3. Em **QSAppDelegate.m**, adicione os seguintes métodos de manipulador. Seu aplicativo agora é atualizado para oferecer suporte a notificações por push. 

        // Registration with APNs is successful
        - (void)application:(UIApplication *)application
        didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;
        
            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }
        
        // Handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }
        
        // Use userInfo in the payload to display an alert.
        - (void)application:(UIApplication *)application
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
        
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
        
            // Create alert with notification content.
            UIAlertController *alertController = [UIAlertController
                                          alertControllerWithTitle:@"Notification"
                                          message:alertString
                                          preferredStyle:UIAlertControllerStyleAlert];
        
            UIAlertAction *cancelAction = [UIAlertAction
                                           actionWithTitle:NSLocalizedString(@"Cancel", @"Cancel")
                                           style:UIAlertActionStyleCancel
                                           handler:^(UIAlertAction *action)
                                           {
                                               NSLog(@"Cancel");
                                           }];
            
            UIAlertAction *okAction = [UIAlertAction
                                       actionWithTitle:NSLocalizedString(@"OK", @"OK")
                                       style:UIAlertActionStyleDefault
                                       handler:^(UIAlertAction *action)
                                       {
                                           NSLog(@"OK");
                                       }];
            
            [alertController addAction:cancelAction];
            [alertController addAction:okAction];
            
            // Get current view controller.
            UIViewController *currentViewController = [[[[UIApplication sharedApplication] delegate] window] rootViewController];
            while (currentViewController.presentedViewController)
            {
                currentViewController = currentViewController.presentedViewController;
            }
            
            // Display alert.
            [currentViewController presentViewController:alertController animated:YES completion:nil];
        
        }

**Rápido**:

1. Adicione arquivo **ClientManager.swift** com o seguinte conteúdo. Substitua _AppUrl %_ com a URL do aplicativo do Azure Mobile back-end.
        
        class ClientManager {
            static let sharedClient = MSClient(applicationURLString: "%AppUrl%")
        }

2. No **ToDoTableViewController.swift**, substitua o `let client` linha que inicializa um `MSClient` com esta linha:

        let client = ClientManager.sharedClient
 
3. No **AppDelegate.swift**, substitua o corpo da `func application` da seguinte maneira:

        func application(application: UIApplication,
          didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
           application.registerUserNotificationSettings(
               UIUserNotificationSettings(forTypes: [.Alert, .Badge, .Sound],
                   categories: nil))
           application.registerForRemoteNotifications()
           return true
        }

2. Em **AppDelegate.swift**, adicione os seguintes métodos de manipulador. Seu aplicativo agora é atualizado para oferecer suporte a notificações por push.
   
        func application(application: UIApplication,
           didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
            ClientManager.sharedClient.push?.registerDeviceToken(deviceToken) { error in
                print("Error registering for notifications: ", error?.description)
            }
        }
        
        func application(application: UIApplication,
           didFailToRegisterForRemoteNotificationsWithError error: NSError) {
            print("Failed to register for remote notifications: ", error.description)
        }
        
        func application(application: UIApplication,
           didReceiveRemoteNotification userInfo: [NSObject: AnyObject]) {
            
            print(userInfo)
            
            let apsNotification = userInfo["aps"] as? NSDictionary
            let apsString       = apsNotification?["alert"] as? String
            
            let alert = UIAlertController(title: "Alert", message: apsString, preferredStyle: .Alert)
            let okAction = UIAlertAction(title: "OK", style: .Default) { _ in
                print("OK")
            }
            let cancelAction = UIAlertAction(title: "Cancel", style: .Default) { _ in
                print("Cancel")
            }
            
            alert.addAction(okAction)
            alert.addAction(cancelAction)
            
            var currentViewController = self.window?.rootViewController
            while currentViewController?.presentedViewController != nil {
                currentViewController = currentViewController?.presentedViewController
            }
            
            currentViewController?.presentViewController(alert, animated: true) {}
            
        }
            

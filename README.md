**Fabric iOS Extension**
===================

If you are looking for a customizble iMessage sticker project with Fabric integrated, see [github.com/Fluffcorn](https://github.com/Fluffcorn/ios-sticker-packs-app).

How to integrate Fabric with your own iOS extension (with API key and build secret isolation for public projects and distribution)
-------------

1. Download the [Fabric frameworks](https://fabric.io/kits/ios/crashlytics/manual-install).

2. Import frameworks into your Xcode project

  - ![Import frameworks to Xcode project](https://raw.githubusercontent.com/ansonl/fabric-ios-extension/master/assets/import-frameworks.gif)

3. Create `fabric.apikey` and `fabric.buildsettings` files in Xcode project directory root. Put your Fabric API key and build secret into the files respectively.
  - ![Create files](https://raw.githubusercontent.com/ansonl/fabric-ios-extension/master/assets/create-files.png)

4. Add the below lines to the project `.gitignore` file if you intend on making the project public. 

    ```
    fabric.apikey
    fabric.buildsecret
    ```

  - ![Add files to .gitignore](https://raw.githubusercontent.com/ansonl/fabric-ios-extension/master/assets/add-files-to-gitignore.gif)

5. Add *Run Script* below to your extension target. *Run Script* is located under the *[ExtensionTarget]* > *Build Phases*. 

    ```
    FABRIC_APIKEY=$(cat ${SRCROOT}/fabric.apikey)
    FABRIC_BUILDSECRET=$(cat ${SRCROOT}/fabric.buildsecret)
    ${SRCROOT}/Fabric.framework/run ${FABRIC_APIKEY} ${FABRIC_BUILDSECRET}
    ```

- ![Add run script](https://github.com/ansonl/fabric-ios-extension/blob/master/assets/add-run-script.gif)

6. Import frameworks into your initial view controller or class.

    ```
    #import <Fabric/Fabric.h>
    #import <Crashlytics/Crashlytics.h>
    ```

7. Add or modify `initWithCoder:` method in your initial view controller or class.

    ```
    - (id)initWithCoder:(NSCoder *)decoder {
        self = [super initWithCoder:decoder];
        if (!self) {
           return nil;
        }
    
        NSURL* resourceURL = [[NSBundle mainBundle] URLForResource:@"fabric.apikey" withExtension:nil];
        NSStringEncoding usedEncoding;
        NSString* fabricAPIKey = [NSString stringWithContentsOfURL:resourceURL usedEncoding:&usedEncoding error:NULL];
        NSCharacterSet* whitespaceToTrim = [NSCharacterSet whitespaceAndNewlineCharacterSet];
        NSString* fabricAPIKeyTrimmed = [fabricAPIKey stringByTrimmingCharactersInSet:whitespaceToTrim];
    
        [Crashlytics startWithAPIKey:fabricAPIKeyTrimmed];
    
        return self;
    }
    ```

8. Build and Run
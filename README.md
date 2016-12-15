# YYText-Extension
Extension for YYText Library

## YYLabelLinkParser 

### Introduction

`YYLabelLinkParser` makes use of `textParser` property in the `YYLabel` to help detect `email`, `mobile number`, meanwhile, a text highlight is assigned to the detected link. When the link is tap, delegate methods which conformed to `YYLabelLinkActionDelegate` will be called.

### Usage

1. Initialize a link parser `YYLabelLinkParser` with optional delegate which confirm to `YYLabelLinkActionDelegate`. When the link is tap, the delegate method will be called.
2. Implement the methods for `YYLabelLinkActionDelegate`.

   * `handleMailAction(_)` is called when user tap the email link in the YYLabel
   * `handleMobileAction(_)` is called when the user tap the mobile link in the YYLabel.

### Example code:

```
class TestViewController: YYLabelLinkActionDelegate {

    @IBOutlet weak var contactLabel: YYLabel!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        contactLabel.attributedText = viewModel.contact
        
        // Create a link parser and assigned it to the yylabel textParser property
        contactLabel.textParser = YYLabelLinkParser(delegate: self)
    }
    
    // MARK: YYLabelLinkActionDelegate
    
    func handleMailAction(string: String) {
        // Called when the user tap the email link
        UIAlertController.showActionSheet(title: "提示".localized,
                                          message: "发现邮箱地址".localized,
                                          inController: self,
                                          actionsTitles: [string, "取消".localized],
                                          actionCallback: {
                                                [weak self]
                                                (_, index) in
                                                    if index == 0 {
                                                    if MFMailComposeViewController.canSendMail() {
                                                        let vc = MFMailComposeViewController()
                                                        vc.setToRecipients([string])
                                                        vc.mailComposeDelegate = self
                                                        self?.present(vc, animated: true, completion: nil)
                                                    }
                                                }
                                          },
                                          completion: nil)
    }
    
    func handleMobileAction(string: String) {
        // Called when the user tap the mobile link
        UIAlertController.showActionSheet(title: "提示".localized,
                                          message: "发现手机号码".localized,
                                          inController: self,
                                          actionsTitles: [string, "取消".localized],
                                          actionCallback: {
                                                (_, index) in
                                                if index == 0 {
                                                    UIApplication.shared.openURL(URL(string: "tel://" + string)!)
                                                }
                                          },
                                          completion: nil)
    }
}
```

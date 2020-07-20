# How to save images from the Photo Library in Swift

# This post is unfinished. If you have found it, please do not follow it!


## I'm sure that a lot of people have needed to do this at some point. Lots of apps need to take an image from the user and save it for access later. This tutorial will show you how to do that.


### Step 1 - user interface
You should always start by presenting the user with a clear way to upload an image, as shown in my app What's Cooking:
![What's Cooking Add](https://cdn.itsnoahevans.co.uk/devblog/imgs/tutorials/save-image-swift/wc-add.png)


You'll need to have, at minimum to do the demo (you'll probably integrate the code in your own way) a UIImageView to show the image later, two buttons for upload and show, and a UITextField to name the file. In my app above, the top text field for the meal name is how we name images, the Table View cell is how we initiate the image picker, and the save button is how we write the data. The image is presented in a separate view controller, which calls for the image when it's loaded so a "show" button is not necessary.


Once you've got a user interface set up - this could be something as simple as two buttons ("Upload Image", "Show Image") and a UIImageView somewhere in your app - we're ready to start the coding.


Make sure you've linked your buttons and the textfield with an outlet (or Table View indexPath if you're going that way) and your UIImageView in your app before continuing. This step is important.


### Step 2 - present the UIImagePicker
The UIImagePicker is a method that we can use to present a system image picker to the user. It's quite simple to use, so let's get started.

Where you see `class ViewController: UIViewController {`, you should add `, UIImagePickerControllerDelegate` to the end. Your final class declaration should look like this:

``` swift
class ViewController: UIViewController, UIImagePickerControllerDelegate {
```

You should also create a new variable. We'll talk more about this later. Add
``` swift
var imgPickCancelled: String = ""
```

Great, that's that done. Next for the image picker itself.
Paste this chunk just after your `var` declaration:
``` swift
let imagePicker = UIImagePickerController()
   internal func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
       if let pickedImage = info[UIImagePickerController.InfoKey.originalImage] as? UIImage {
           if let data = pickedImage.pngData() {
               // Create URL
                   let documents = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
               let fileName = yourTextField.text! + ".png"
                   let url = documents.appendingPathComponent(fileName)
               do {
                       // Write to Disk
                       try data.write(to: url)

                       // Return URL
                       returnURL = url

                   } catch {
                       print("Unable to Write Data to Disk (\(error))")
                       imgPickCancelled = "NO_IMAGE_CHOSEN"
                   }

           }

       }
       else {
           print("Data is empty: no image picked.")
           imgPickCancelled = "NO_IMAGE_CHOSEN"
           return
       }

       dismiss(animated: true, completion: nil)

   }

   func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
       dismiss(animated: true, completion: nil)
       print("Image selection was cancelled")
       imgPickCancelled = "NO_IMAGE_CHOSEN"
   }
```

Okay, so that's a huge chunk of code. Let's break it down.

* Firstly, we define the image picker with `let imagePicker = UIImagePickerController()`.
* The next few lines just get everything ready.
* `if let pickedImage = info[UIImagePickerController.InfoKey.originalImage] as? UIImage {` makes the variable `pickedImage` contain the image the user saved, as a `UIImage`.
* Next, `let documents = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]` gets the File Manager ready to save the image.
* The next two lines create the filename for the image by putting the user's text field input and the

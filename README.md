
# nft-generator-py

![preview](https://github.com/Jon-Becker/nft-generator-py/blob/main/preview.png?raw=true)

nft-generator-py is a python based NFT generator which programatically generates unique images using weighted layer files. The program is simple to use, and new layers can  be added by adding a new layer object and adding names, weights, and image files to the object.
You can [View The Demo](https://jbecker.dev/demos/nft-generator-py) here.

## Usage
As of v2.0.0, nft-generator-py will use the argparse library in order to support external configuration files and won't require users to interact with the python files themselves.

1. Install requirements: `python3 -m pip install -r requirements.txt`
2. Make a configuration JSON file. See the configuration section below for specifications.
3. Add layer files into the `/trait-layers` folder.
4. Run the command `python3 generate.py --amount AMOUNT --config CONFIG`
   
   where:
   1. `AMOUNT` is the amount of images to generate
   2. `CONFIG` is the path pointing to a `.json` file containing valid program configuration.

## How it works
- A call to `generate_unique_images(amount, config)` is made, which is the meat of the application where all the processing happens.
- The `config` object is read and for each object in the `layers` list, random values are selected and checked for uniqueness against all previously generated metadata files.
- Once we have `amount` unique tokens created, we layer them against eachother and output them and their metadata to their respective folders, `./metadata` and `./images`.

### Configuration
```
{
    "layers": [
      {
        "name": "Background",
        "values": ["Blue", "Green", "Purple Mix", "Fade Away", "Neon Mix", "Pattern", "Gradient", "Stacked Wall", "Mixture", "Leaves"],
        "trait_path": "./trait-layers/backgrounds",
        "filename": ["Blue", "Green", "Purple Mix", "Fade Away", "Neon Mix", "Pattern", "Gradient", "Stacked Wall", "Mixture", "Leaves"],
        "weights": [10,10,10,10,10,10,10,10,10,10]
      },
      {
        "name": "Foreground",
        "values": ["Award-One", "Award-Two", "Award-Three", "Award-Four", "Award-Five", "Award-Six", "Award-Seven", "Award-Eight", "Award-Nine", "Award-Ten"],
        "trait_path": "./trait-layers/foreground",
        "filename": ["Awd-one", "Awd-two", "Awd-three", "Awd-four", "Awd-five", "Awd-six", "Awd-seven", "Awd-eight", "Awd-nine", "Awd-ten"],
        "weights": [10,10,10,10,10,10,10,10,10,10]
      },
      {
        "name": "Fundtext",
        "values": ["Fund Raiser"],
        "trait_path": "./trait-layers/text",
        "filename": ["donation"],
        "weights": [100]
      }
    ],
    "incompatibilities": [
      {
        "layer": "Background",
        "value": "Leaves",
        "incompatible_with": ["Award-Five"],
        "default": {
          "value": "Default Incompatibility",
          "filename": "./trait-layers/foreground/Awd-five"
        }
      }
    ],
    "baseURI": ".",
    "name": "NFT_",
    "description": "Project Fund Raiser."
}
```

The `config` object is a dict that contains configuration instructions that can be changed to produce different outputs when running the program. Within metadata files, images are named using the configuration's `name` parameter, and described using the `description` parameter. 
- In ascending order, Id's are appended to the `name` resulting in NFT metadata names such as NFT NFT_1.
- For the sake of our fund raiser tutorial tokenId's won't be assigned to the images at the point the are being generated. But will be assigned to any image selected when the are being minted on the contract.  
- Id's are padded to the largest amount generated. IE, generating 999 objects will result in names NFT_1, using the above configuration, and generating 1000 objects will result in NFT_999.
- As of `v1.0.2`, padding filenames has been removed.

The `layers` list contains `layer` objects that define the layers for the program to use when generating unique images. Each `layer` has a name,  which will be displayed as an attribute, values, trait_path, filename, and weights.
- `trait_path` refers to the path where the image files in `filename` can be found. Please note that filenames omit .png, and it will automatically be prepended.
- `weight` corresponds with the percent chance that the specific value that weight corresponds to will be selected when the program is run. The weights must add up to 100, or the program will fail.

The `incompatibilities` list contains an object that tells the program what layers are incompatible with what. In the above configuration, A Leaves Background `layer` will *never* be generated with Award-Five.
- `layer` refers to the targeted layer.
- `value` is the value of the layer that is incompatible with attributes within the `incompatible_with` list.
- `incompatible_with` is the list of incompatible layers that will never be selected when `layer` has attribute `value`.
- An optional `default` object can be provided to each incompatibility. This object will be selected 100% of the time if present and an incompatible layer is selected. The `default` object has a `value` and `filename` attribute.
  - `value` is the name of the default selection which will be displayed in the metadata.
  - `filename` is the path to the image file that will be used as the default selection.
  
- For This tutorial, the generated images in the `/images` and metadata in the `/metadata` directories will be copied to there respective directory Where our Fund Raiser Contract is initiated so the could be interacted with and Uploaded to the IPFS.[Crowdfunding Smart Contract Development Tutorial On Celo With Eth-Brownie](http://place-link-here) a link to the project repo would be provided.


#### Troubleshooting
- All images should be in .png format.
- All images should be the same size in pixels, IE: 1000x1000.
- The weight values for each attribute should add up to equal 100.

### Credits
This project is completely coded by [Jonathan Becker](https://jbecker.dev), using no external libraries.


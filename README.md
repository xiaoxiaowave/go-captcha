<div align="center">
<img width="120" style="padding-top: 50px; margin: 0;" src="http://47.104.180.148/go-captcha/gocaptcha_logo.svg?v=1"/>
<h1 style="margin: 0; padding: 0">Go Captcha</h1>
<p>Behavior Security Captcha</p>
<a href="https://goreportcard.com/report/github.com/wenlng/go-captcha"><img src="https://goreportcard.com/badge/github.com/wenlng/go-captcha"/></a>
<a href="https://godoc.org/github.com/wenlng/go-captcha"><img src="https://godoc.org/github.com/wenlng/go-captcha?status.svg"/></a>
<a href="https://github.com/wenlng/go-captcha/releases"><img src="https://img.shields.io/github/v/release/wenlng/go-captcha.svg"/></a>
<a href="https://github.com/wenlng/go-captcha/blob/v2/LICENSE"><img src="https://img.shields.io/github/license/wenlng/go-captcha.svg"/></a>
<a href="https://github.com/wenlng/go-captcha"><img src="https://img.shields.io/github/stars/wenlng/go-captcha.svg"/></a>
<a href="https://github.com/wenlng/go-captcha"><img src="https://img.shields.io/github/last-commit/wenlng/go-captcha.svg"/></a>
</div>

<br/>

> English | [中文](README_zh.md)

<p style="text-align: center"><a href="https://github.com/wenlng/go-captcha">Go Captcha</a> is a behavior security CAPTCHA, which implements text click verification, slide verification and rotation verification.</p>

<p style="text-align: center"> ⭐️ If it helps you, please give a star.</p>

<div align="center"> 
    <img src="http://47.104.180.148/go-captcha/go-captcha-v2.jpg" alt="Poster">
</div>

<br/>

- GoCaptcha：[https://github.com/wenlng/go-captcha](https://github.com/wenlng/go-captcha)
- GoCaptcha Document：[http://gocaptcha.wencodes.com](http://gocaptcha.wencodes.com)
- Go Example：[https://github.com/wenlng/go-captcha-example](https://github.com/wenlng/go-captcha-example)
- Go Assets：[https://github.com/wenlng/go-captcha-assets](https://github.com/wenlng/go-captcha-assets)
- Vue Package：[https://github.com/wenlng/go-captcha-vue](https://github.com/wenlng/go-captcha-vue)
- React Package：[https://github.com/wenlng/go-captcha-react](https://github.com/wenlng/go-captcha-react)
- Online Demo：[http://gocaptcha.wencodes.com/demo](http://gocaptcha.wencodes.com/demo)
- ...
-
<br/>

> ⚠️ v1 will be removed after v2.1, please upgrade to v2 as soon as possible


## Install Captcha Module
```shell
$ go get -u github.com/wenlng/go-captcha/v2@v2.0.0-beta.1
```

## Import Captcha Module
```go
package main

import "github.com/wenlng/go-captcha/v2"

func main(){
   // ...
}
```

<br />

## 🖖 Click Mode Captcha
### Quick use
```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"log"
	"io/ioutil"

	"github.com/golang/freetype"
	"github.com/golang/freetype/truetype"
	"github.com/wenlng/go-captcha/v2/base/option"
	"github.com/wenlng/go-captcha/v2/click"
	"github.com/wenlng/go-captcha/v2/base/codec"
)

var textCapt click.Captcha

func init() {
	textCapt = click.New(
		click.WithRangeLen(option.RangeVal{Min: 4, Max: 6}),
		click.WithRangeVerifyLen(option.RangeVal{Min: 2, Max: 4}),
	)

	fontN, err := loadFont("../resources/fzshengsksjw_cu.ttf")
	if err != nil {
		log.Fatalln(err)
	}

	bgImage, err := loadPng("../resources/bg.png")
	if err != nil {
		log.Fatalln(err)
	}

	textCapt.SetResources(
		click.WithChars([]string{
			"1A",
			"5E",
			"3d",
			"0p",
			"78",
			"DL",
			"CB",
			"9M",
			// ...
		}),
		click.WithFonts([]*truetype.Font{
			fontN,
		}),
		click.WithBackgrounds([]image.Image{
			bgImage,
		}),
	)
}

func loadPng(p string) (image.Image, error) {
	imgBytes, err := ioutil.ReadFile(p)
	if err != nil {
		return nil, err
	}
	return codec.DecodeByteToPng(imgBytes)
}

func loadFont(p string) (*truetype.Font, error) {
	fontBytes, err := ioutil.ReadFile(p)
	if err != nil {
		panic(err)
	}
	return freetype.ParseFont(fontBytes)
}


func main() {
	captData, err := textCapt.Generate()
	if err != nil {
		log.Fatalln(err)
	}

	dotData := captData.GetData()
	if dotData == nil {
		log.Fatalln(">>>>> generate err")
	}

	dots, _ := json.Marshal(dotData)
	fmt.Println(">>>>> ", string(dots))

	err = captData.GetMasterImage().SaveToFile("../resources/master.jpg", option.QualityNone)
	if err != nil {
		fmt.Println(err)
	}
	err = captData.GetThumbImage().SaveToFile("../resources/thumb.png")
	if err != nil {
		fmt.Println(err)
	}
}
```

### Create instance method
- click.New()
- click.NewWithShape()

### Configuration options
click.New(click.WithXxx(), ...)
- click.WithImageSize(option.Size)
- click.WithRangeLen(option.RangeVal) 
- click.WithRangeAnglePos([]option.RangeVal) 
- click.WithRangeSize(option.RangeVal)
- click.WithRangeColors([]string) 
- click.WithDisplayShadow(bool) 
- click.WithShadowColor(string) 
- click.WithShadowPoint(option.Point)
- click.WithImageAlpha(float32) 
- click.WithUseShapeOriginalColor(bool)

- click.WithThumbImageSize(option.Size)
- click.WithRangeVerifyLen(option.RangeVal)
- click.WithRangeThumbSize(option.RangeVal)
- click.WithRangeThumbColors([]string)
- click.WithRangeThumbBgColors([]string)
- click.WithIsThumbNonDeformAbility(bool)
- click.WithThumbBgDistort(int) 
- click.WithThumbBgCirclesNum(int) 
- click.WithThumbBgSlimLineNum(int) 


### Set resources
xxx.SetResources(click.WithXxx(), ...)
- click.WithChars([]string) 
- click.WithShapes(map[string]image.Image) 
- click.WithFonts([]*truetype.Font) 
- click.WithBackgrounds([]image.Image) 
- click.WithThumbBackgrounds([]image.Image) 

### Captcha Data
- GetData() map[int]*Dot
- GetMasterImage() imagedata.JPEGImageData
- GetThumbImage() imagedata.PNGImageData

<br />

## 🖖 Slide Mode Captcha
### Quick use
```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"log"
	"io/ioutil"

	"github.com/wenlng/go-captcha/v2/base/option"
	"github.com/wenlng/go-captcha/v2/slide"
	"github.com/wenlng/go-captcha/v2/base/codec"
)

var slideTileCapt slide.Captcha

func init() {
	slideTileCapt = slide.New()

	bgImage, err := loadPng("../resources/bg.png")
	if err != nil {
		log.Fatalln(err)
	}

	bgImage1, err := loadPng("../resources/bg1.png")
	if err != nil {
		log.Fatalln(err)
	}

	graphs := getSlideTileGraphArr()

	slideTileCapt.SetResources(
		slide.WithGraphImages(graphs),
		slide.WithBackgrounds([]image.Image{
			bgImage,
			bgImage1,
		}),
	)
}

func getSlideTileGraphArr() []*slide.GraphImage {
	tileImage1, err := loadPng("../resources/tile-1.png")
	if err != nil {
		log.Fatalln(err)
	}

	tileShadowImage1, err := loadPng("../resources/tile-shadow-1.png")
	if err != nil {
		log.Fatalln(err)
	}
	tileMaskImage1, err := loadPng("../resources/tile-mask-1.png")
	if err != nil {
		log.Fatalln(err)
	}

	return []*slide.GraphImage{
		{
			OverlayImage: tileImage1,
			ShadowImage:  tileShadowImage1,
			MaskImage:    tileMaskImage1,
		},
	}
}

func main() {
	captData, err := slideTileCapt.Generate()
	if err != nil {
		log.Fatalln(err)
	}

	blockData := captData.GetData()
	if blockData == nil {
		log.Fatalln(">>>>> generate err")
	}

	block, _ := json.Marshal(blockData)
	fmt.Println(">>>>>", string(block))

	err = captData.GetMasterImage().SaveToFile("../resources/master.jpg", option.QualityNone)
	if err != nil {
		fmt.Println(err)
	}
	err = captData.GetTileImage().SaveToFile("../resources/thumb.png")
	if err != nil {
		fmt.Println(err)
	}
}

func loadPng(p string) (image.Image, error) {
	imgBytes, err := ioutil.ReadFile(p)
	if err != nil {
		return nil, err
	}
	return codec.DecodeByteToPng(imgBytes)
}
```


### Create instance method
- slide.New()
- slide.NewWithRegion() 


### Configuration options
slide.New(slide.WithXxx(), ...)
- slide.WithImageSize(*option.Size)
- slide.WithImageAlpha(float32) 
- slide.WithRangeGraphSize(val option.RangeVal) 
- slide.WithRangeGraphAnglePos([]option.RangeVal) 
- slide.WithGenGraphNumber(val int)
- slide.WithEnableGraphVerticalRandom(val bool) 
- slide.WithRangeDeadZoneDirections(val []DeadZoneDirectionType) 


### Set resources
xxx.SetResources(slide.WithXxx(), ...)
- slide.WithBackgrounds([]image.Image) 
- slide.WithGraphImages(images []*GraphImage)

### Captcha Data
- GetData() *Block
- GetMasterImage() imagedata.JPEGImageData
- GetTileImage() imagedata.PNGImageData


<br />

## 🖖 Rotate Mode Captcha
### Quick use
```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"log"
	"io/ioutil"

	"github.com/wenlng/go-captcha/v2/rotate"
	"github.com/wenlng/go-captcha/v2/base/codec"
)

var rotateCapt rotate.Captcha

func init() {
	rotateCapt = rotate.New()

	bgImage, err := loadPng("../resources/bg.png")
	if err != nil {
		log.Fatalln(err)
	}

	bgImage1, err := loadPng("../resources/bg1.png")
	if err != nil {
		log.Fatalln(err)
	}

	rotateCapt.SetResources(
		rotate.WithImages([]image.Image{
			bgImage,
			bgImage1,
		}),
	)
}

func main() {
	captData, err := rotateCapt.Generate()
	if err != nil {
		log.Fatalln(err)
	}

	blockData := captData.GetData()
	if blockData == nil {
		log.Fatalln(">>>>> generate err")
	}

	block, _ := json.Marshal(blockData)
	fmt.Println(">>>>>", string(block))

	err = captData.GetMasterImage().SaveToFile("../resources/master.png")
	if err != nil {
		fmt.Println(err)
	}
	err = captData.GetThumbImage().SaveToFile("../resources/thumb.png")
	if err != nil {
		fmt.Println(err)
	}
}

func loadPng(p string) (image.Image, error) {
	imgBytes, err := ioutil.ReadFile(p)
	if err != nil {
		return nil, err
	}
	return codec.DecodeByteToPng(imgBytes)
}
```


### Create instance method
- rotate.New()


### Configuration options
rotate.New(rotate.WithXxx(), ...)
- rotate.WithImageSquareSize(val int) 
- rotate.WithRangeAnglePos(vals []option.RangeVal)
- rotate.WithRangeThumbImageSquareSize(val []int) 
- rotate.WithThumbImageAlpha(val float32)


### Set resources
xxx.SetResources(rotate.WithXxx(), ...)
- rotate.WithBackgrounds([]image.Image)

### Captcha Data
- GetData() *Block
- GetMasterImage() imagedata.PNGImageData
- GetThumbImage() imagedata.PNGImageData

<br/>

## Captcha Image Data
### JPEGImageData object method
- Get() image.Image
- ToBytes() []byte
- ToBytesWithQuality(imageQuality int) []byte 
- ToBase64() string
- ToBase64WithQuality(imageQuality int) string
- SaveToFile(filepath string, quality int) error


### PNGImageData object method
- Get() image.Image 
- ToBytes() []byte
- ToBase64() string 
- SaveToFile(filepath string) error

<br/>


> Buy the author coffee: [http://witkeycode.com/sponsor](http://witkeycode.com/sponsor)

<br/>

## LICENSE
Go Captcha source code is licensed under the Apache Licence, Version 2.0 [http://www.apache.org/licenses/LICENSE-2.0.html](http://www.apache.org/licenses/LICENSE-2.0.html)

# ProyectoAngularTutorial02

1. creamos el proyecto

```
ng new proyecto-tutorial02-s3-angular

seleccionamos routing and CSS
```

2. Instalamos las dependencias:
```
npm install --save @aws-sdk/client-s3
```

3. configuramos polyfills.ts
```
(window as any).global = window;
```

4. en este archivo environtment.ts definimos las credenciales de acceso a AWS - S3
 ```
export const environment = {
  production: false,
  AWS_BUCKET:"tutorial02-bucket",
  AWS_ACCESS_KEY_ID: "XXXXXXXXXXXXXXX",
  AWS_SECRET_ACCESS_KEY: "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  AWS_REGION: "us-east-1"
};
 ```

 5. desarrollamos la funcionalidad, creamos el servicio que se conectara a AWS y subira el archivo a S3
 ```
ng g service s3-service
 ```

implementamos el servicio:
 ```
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { environment } from 'src/environments/environment';

@Injectable({
  providedIn: 'root'
})
export class S3ServiceService {

  private bucket: S3Client;

  constructor(private http: HttpClient) {
    this.bucket = new S3Client(
      {
        credentials: {
          accessKeyId: environment.AWS_ACCESS_KEY_ID,
          secretAccessKey: environment.AWS_SECRET_ACCESS_KEY,
        },
        region: environment.AWS_REGION,
      }
    );
   }

   async uploadFile(file: File) {

    const contentType = file.type;
  
    const params = {
      Bucket: 'tutorial02-bucket',
      Key: "documents/"+file.name,
      Body: file,
      //ACL: 'public-read',
      ContentType: contentType
    };

    try {
      const response = await this.bucket.send(new PutObjectCommand(params));
      console.log("SUCCESS", response);
    } catch(error) {
      console.log("FAILURE", error);
    }
   
  }
  
}

 ```

7. configuramos el acceso CORS en AWS - S3
```
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

8. configuramos app.component.html
```
<router-outlet>
  <div>
    <label>
      <input type="file" name="fileUpload" (change)="onFileSelect($event)" />
    </label>
  </div>
</router-outlet>
```

9. configuramos app.component.ts
 ```
 import { Component } from '@angular/core';
import { S3ServiceService } from './s3-service.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'aws-s3-bucket';
  constructor(private s3Service: S3ServiceService) { }


  onFileSelect(e: any) {
    // with presigned url
    //this.s3Service.uploadFileWithPreSignedURL(e.target.files[0]);

    // without presigned url
    this.s3Service.uploadFile(e.target.files[0]);
  }
}
```

10. agregamos HttpClientModule en app.module 
```
import { HttpClientModule } from "@angular/common/http";

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})

```

11. compilamos y levantamos el servidor:
 ```
npm i
ng serve
 ```








 ```
echo "# proyecto-tutorial02-s3-angular" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git remote add origin https://github.com/magzupao/proyecto-tutorial02-s3-angular.git
git push -u origin main
 ```





este tutorial a seguido este articulo
https://prodevhub.com/how-to-upload-files-to-s3-from-the-browser-angular/


This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 14.2.3.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The application will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.

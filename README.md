# Firebase Database Cheatsheet
cheatsheet untuk firebase Database ReactJS
***
## Daftar Isi
- [Firebase Config](#file-config)
- [Multiple Firebase Project](#multiple-firebase-project)
- [Retrieve Data](#retieve-data)
  - [`on()`](#on())
  - [`once()`](#once())
- [Commands](#Commands)
  - [`push()`](#push())
  - [`set()`](#set())
  - [`update()`](#update())
  - [`delete()`](#delete())
- [Checking Data](#checking-data)
- [Callback and Promise](#callback-and-promise)
  - [`Callback`](#callback)
  - [`Promise`](#promise)
- [Get firebase data key](#get-firebase-data-key)
  

  
***
## Firebase Config
>### konfigurasi standar 
file `firebaseConfig.jsx`
``` jsx
const config = {
	apiKey: "_from_your_firebase_project_", 
	authDomain: "_from_your_firebase_project_",
	databaseURL: "_from_your_firebase_project_",
	projectId: "_from_your_firebase_project_",
	storageBucket: "_from_your_firebase_project_",
	messagingSenderId: "_from_your_firebase_project_"
};
export default config;
```
***
file `firebaseRef.jsx`
``` jsx
import firebase from '@firebase/app';
import '@firebase/database'; // jika hanya ingin menggunakan firebase database
import config from './firebaseConfig'

firebase.initializeApp( config );
export const rootRef = firebase.databas( ).ref( );
```
***
>### alternatif config untuk beberapa kasus
file `firebaseInit.jsx`
``` jsx
import firebase from "@firebase/app";
import "@firebase/database"; // jika hanya ingin menggunakan firebase database
import config from "./firebaseConfig";

export default (!firebase.apps.length
  ? firebase.initializeApp(config)
  : firebase.app());
```
file `firebaseRef.jsx`
``` jsx
import firebase from "./firebaseInit";

const rootRef = firebase.database( ).ref( )
```
***
## Multiple Firebase Project
``` jsx
import firebase from '@firebase/app';
import "@firebase/database"; // jika hanya ingin menggunakan firebase database
import config from "./firebaseConfig";

firebase.initializeApp( config );

const configOther = {
    databaseURL: "_from_your_other_firebase_project_"
};

var firebaseOther = firebase.initializeApp(configOther, "other");
export const rootRefOther = firebaseOther.database().ref()
```
***
## Retrieve Data
### `on()` 
menerima data secara realtime
``` js
firebaseRef.on('value', snap => {
	let tmp = [];
	snap.forEach(shot => {
		tmp.push({
			// data1: shot
			// 	.val( )
			// 	.data1,
			// data2: shot
			// 	.val( )
			// 	.data2,
			// data3: shot
			// 	.val( )
			// 	.data3,
			...shot.val( )
		})
	});
	this.setState({ master: tmp })
})
```
### `once()`
menerima data hanya sekali dan tidak realtime
```js
firebaseRef.once('value', snap => {
	let tmp = [ ];
	snap.forEach(shot => {
		tmp.push({
			// data1: shot
			// 	.val( )
			// 	.data1,
			// data2: shot
			// 	.val( )
			// 	.data2,
			// data3: shot
			// 	.val( )
			// 	.data3,
			...shot.val( )
		})
	});
	this.setState({ master: tmp })
})
```
***
## Commands
perintah yang digunakan untuk memanipulasi database
### `push()`
menambahkan data dengan key yang dibuat secara otomatis oleh `firebase`
```js
PushData = () => {
	const content = {
	        data1: null,
		data2: null,
		data3: null
	};
	firebaseRef.push(content)
}
```
### `set()`
menambahkan data dengan key yang dibuat secara kustom/primary key yang ada
```js
SetData = ( PRIMARY_KEY ) => {
	const content = {
		data1: null,
		data2: null,
		data3: null
	};
	firebaseRef.child( PRIMARY_KEY ).set(content)
}
```
### `update()`
memperbarui data dengan primary key yang ada
```js
UpdateData = ( PRIMARY_KEY ) => {
	const content = {
		data1: null,
		data2: null,
		data3: null
	};
        firebaseRef.child( PRIMARY_KEY ).update( content )
}
```
### `delete()`
menghapus data dengan primary key yang ada
```js
DeleteData = ( PRIMARY_KEY ) => {
	const content = {
		data1: null,
		data2: null,
		data3: null
	};
        tasksRef.child( PRIMARY_KEY ).remove();
}
```
***
## Checking Data
mengecek terdapat data atau tidak
```js
_Checking = ( info ) => {
	firebaseRef_setCHILD( info ).once('value', snapshot => {
		if (snapshot.exists( )) {
			//if exists
		} else {
			//if not
		}
	})
}
```
***
## Callback and Promise
### `Callback`
```js
_Callback = ( ) => {
        const content = {
		data1: null,
		data2: null,
		data3: null
	};
	firebaseRef.set(content, 
                ( error ) => {
			if ( error ) {
				//if write fail
			} else {
				//if write success
			}
		})
}
```
### `Promise`
```js
_Promise = ( ) => {
	const content = {
		data1: null,
		data2: null,
		data3: null
	};
	firebaseRef
		.update( content )
		.then(( ) => {
			//if write success
		})
		.catch(( error ) => {
			//if write fail
		})
}
```
***
## Get `firebase` data key
```js
const newPostKey = firebaseRef.child('user').push().key;
```
***
## Online Status
file `firebaseRef.jsx`
```js
export const connectedRef = rootRef.child('.info/connected');
export const myConnectionsRef = rootRef.child('player/1/connections');
export const lastOnlineRef = rootRef.child('player/1/lastOnline');
```
on `componentWillMount()`
```js
// firebaseRefDC.onDisconnect().set("I disconnected!");
connectedRef.on('value', (snap)=> {
         if (snap.val() === true) {
                // We're connected (or reconnected)! Do anything here that should happen only if online (or on reconnect)
                 let con = myConnectionsRef.push();
                      
                // When I disconnect, remove this device
                myConnectionsRef.onDisconnect().set(false);
                      
                 // Add this device to my connections list
                 // this value could contain info about the device or a timestamp too
                myConnectionsRef.set(true);
                      
                // When I disconnect, update the last time I was seen online
                lastOnlineRef.onDisconnect().set(firebase.database.ServerValue.TIMESTAMP);
        }
});
```
***
## Firebase Queries
contoh data
```json
{
	"mahasiswa": {
		"1": {
			"nama" : "ahmad dazzle",
			"nim" : 16102201,
			"kelas" : "c",
			"prodi" : "informatika",
			"semester" : 6
		},
		"2": {
			"nama" : "ahmad suryana",
			"nim" : 16102202,
			"kelas" : "a",
			"prodi" : "elektro",
			"semester" : 4 
		},
		"3": {
			"nama" : "eko batrider",
			"nim" : 16102203,
			"kelas" : "c",
			"prodi" : "informatika",
			"semester" : 6
		},
		"4": {
			"nama" : "surya adi",
			"nim" : 16102204,
			"kelas" : "b",
			"prodi" : "informatika",
			"semester" : 4 
		},
		"5": {
			"nama" : "bayu adi",
			"nim" : 16102205,
			"kelas" : "a",
			"prodi" : "elektro",
			"semester" : 4 
		},
		"6": {
			"nama" : "ajie warlock",
			"nim" : 16102206,
			"kelas" : "d",
			"prodi" : "informatika",
			"semester" : 4 
		},
		"7": {
			"nama" : "handoko invoker",
			"nim" : 16102207,
			"kelas" : "d",
			"prodi" : "informatika",
			"semester" : 4 
		},
		"8": {
			"nama" : "ajie adi",
			"nim" : 16102208,
			"kelas" : "b",
			"prodi" : "informatika",
			"semester" : 4 
		},
		"9": {
			"nama" : "anisa maiden",
			"nim" : 16102209,
			"kelas" : "c",
			"prodi" : "informatika",
			"semester" : 6
		},
		"10": {
			"nama" : "fitria enchantress",
			"nim" : 16102210,
			"kelas" : "a",
			"prodi" : "elektro",
			"semester" : 4 
		}
	}
}
```
`select mahasiswa dengan id=1`
```js
firebaseRef.child('mahasiswa').child('1');
//alternatif
firebaseRef.child('mahasiswa/1');
```
`select mahasiswa dengan nim=16102201`
```js
firebaseRef.child('mahasiswa').orderByChild('nim').equalTo(16102201);
```
`select 5 data mahasiswa dari awal`
```js
firebaseRef.child('mahasiswa').limitToFirst(5)
```
`select 5 data mahasiswa dari akhir`
```js
firebaseRef.child('mahasiswa').limitToLast(5)
```
`select mahasiswa dengan nama depan "ahmad"`
```js
firebaseRef.child('mahasiswa').orderByChild('nama').startAt('ahmad').endAt('ahmad\uf8ff')
```
`select mahasiswa dengan semester < 6`
```js
firebaseRef.child('mahasiswa').orderByChild('semester').endAt(5)
```
`select mahasiswa dengan semester > 4`
```js
firebaseRef.child('mahasiswa').orderByChild('semester').startAt(4)
```
`select mahasiswa dengan semester antara 2 dan 6`
```js
firebaseRef.child('mahasiswa').orderByChild('semester').startAt(2).endAt(6)
```
`select mahasiswa semester 4 dan prodinya elektro`
```js
//belum sempurna
firebaseRef.child('mahasiswa').orderByChild('semester').equalTo(4).on('value', snap => {
        snap.forEach(shot => {
                if(shot.val( ).prodi==='elektro'){
                        console.log(shot.val( ));
                }
        });
})
```

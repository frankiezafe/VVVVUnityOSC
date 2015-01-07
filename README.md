VVVVUnityOSC
============

OSC methods for Unity, that allows **bunlde** management (openframeworks OSC)

To make this run in release, copy **I18N.dll** & **I18N.West.dll** in Assets\Plugins folder from your Unity project.
On my computer, they are located in *C:\Program Files (x86)\Unity\Editor\Data\Mono\lib\mono\2.0*

Code by Yoshinori Kawasaki, found in VVVV SDK > https://github.com/vvvv/vvvv-sdk/, [/common/src/core/Utils/OSC]

## To get it working in unity:

	using UnityEngine;
	using System;
	using System.Threading;
	using System.Net;
	using System.Net.Sockets;
	using System.Collections;
	using System.Collections.Generic;
	using System.Text;

	using VVVV_OSC;

	public class OSCmediadays : MonoBehaviour {

		private Thread thread;
		private OSCReceiver oscin;

		void Start () {
			oscin = new OSCReceiver( [YOU PORT] );
			thread = new Thread( new ThreadStart( UpdateOSC ) );
			thread.Start();
		}

		void OnApplicationQuit() {
			oscin.Close ();
			thread.Interrupt ();
			if( !thread.Join(2000) ) { 
				thread.Abort();
			}
		}

		void UpdateOSC() {
			while( true ) {
				OSCPacket msg = oscin.Receive ();
				if ( msg != null ) {
					if ( msg.IsBundle() ) {
						OSCBundle b = (OSCBundle) msg;
						foreach( OSCPacket subm in b.Values ) {
							parseMessage( subm );
						}
					} else {
						parseMessage( msg );
					}
				}
				Thread.Sleep( 5 );
			}
		}

		void parseMessage( OSCPacket msg ) { 
			Debug.Log( "message with address: " msg.Address );
			// get a value:
			int v = (int) msg.Values[ 0 ];
		}

		void Update() {
		}
	}

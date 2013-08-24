WaveSample
==========

かなり色々適当ですが,とりあえず. 波の式にぶち込んでるだけですね.

3dconvto2d.hはhttps://github.com/AeLP/3Dconvto2D へ.
#include "DxLib.h"//DxLibに頼る
#include "math.h"//sin,cos計算用
#include "3dconvto2d.h"//自作3Dから2Dへの変換関数群


	// 入力状態
	int Input, EdgeInput;
//カメラ制御用変数(DXLib用)
	float VRotate = 0.0f ;
	float HRotate = 200.0f ;
	float TRotate = 300.0f ;
	float xLocate = 300.0f ;
	float yLocate = 2300.0f ;
	float zLocate = 300.0f ;
#define PI 3.1415926

int WINAPI WinMain( HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow )
{

//一辺の長さがLengthの立方体に波の立体構造をかく
//NUMの数だけ要素つくることにする

	double pi= 3.14f;
	int ModelHandle[4];
	int ghandle[10];
	int thandle;
	int ScDx[200][200];
	int ScDy[200][200];
	int flag[200][200];
	float A=0.75;//振幅//1でLengthまであがる
	float T=0.75;//周期
	float R=1.0;//波長
	int count=0;
	int mode=0;//グラフのmode
	int mode2=0;//アニメーション
	float jikan=0;//時間分
	#define NUM 150 //グラフの個数//Max200
	#define Length 2000
	bool calced=FALSE;
	s_Pos AA[200][200];
					int a=0;
				int b=0;


	
	ChangeWindowMode(TRUE);
	
	// ＤＸライブラリの初期化
	if( DxLib_Init() < 0 )
	{
		// エラーが発生したら直ちに終了
		return -1 ;
	}
	
	// 描画先を裏画面にする
	SetDrawScreen( DX_SCREEN_BACK ) ;

	// Ｚバッファを有効にする
	SetUseZBuffer3D( TRUE ) ;

	// Ｚバッファへの書き込みを有効にする
	SetWriteZBuffer3D( TRUE ) ;

	// ＥＳＣキーが押されるかウインドウが閉じられるまでループ
	while( ProcessMessage() == 0 && CheckHitKey( KEY_INPUT_ESCAPE ) == 0 )
	{
		{
			
         int i ;

         // パッド１とキーボードから入力を得る
         i = GetJoypadInputState( DX_INPUT_KEY_PAD1 ) ;

         // エッジを取った入力をセット
         EdgeInput = i & ~Input ;

         // 入力状態の保存
         Input = i ;

		}
		// 画面をクリア
		ClearDrawScreen() ;
		SetCameraPositionAndTarget_UpVecY( VGet(CamPos.x,CamPos.y,CamPos.z), VGet(Target.x,Target.y,Target.z));

			{//軸の描画
			// ３Ｄ空間上に線分を描画する
			DrawLine3D( VGet( 0.0f, 0.0f, 0.0f ), VGet( 4000.0f, 0.0f, 0.0f ), GetColor( 255,0,0 ) ) ;//x軸Red
			// ３Ｄ空間上に線分を描画する
			DrawLine3D( VGet( 0.0f, 0.0f, 0.0f ), VGet( 0.0f, 4000.0f, 0.0f ), GetColor( 0,255,0 ) ) ;//y軸Green
			// ３Ｄ空間上に線分を描画する
			DrawLine3D( VGet( 0.0f, 0.0f, 0.0f ), VGet( 0.0f, 0.0f, 4000.0f ), GetColor( 0,0,255 ) ) ;//z軸Blue
			}

				//重いよ
			if( ( EdgeInput & PAD_INPUT_C ) != 0 ){
				if(calced==FALSE) calced=TRUE;
				if(calced==TRUE) calced=FALSE;
			}

			if(calced==FALSE){//一度目は計算を行う
				for(int i=0;i<NUM;i++){
					for(int j=0; j<NUM; j++){
						flag[i][j]=0;
					}
				}
				for(int i=0;i<NUM;i++){
					for(int j=0; j<NUM; j++){
						float ii=i; float jj=j;
						AA[i][j].x=60*(float)ii*Length/NUM;
						AA[i][j].z=60*(float)jj*Length/NUM;
						AA[i][j].y=20*Length*A*((double)sin(15*2*pi*((jikan+ii)/T-jj/R)));
						
						//バグ回避
						//if((AA[i][j].y>20*Length*A)||(AA[i][j].y<-1*Length*20*A)) flag[i][j]=1;
					}
				}
				//calced=TRUE;//以降計算は行わない
				count++;
				if(count>5){
					jikan=jikan+(T/240);
					count=0;
				}
			}
		

			//2Dへの変換処理は毎回行う
				for(int i=0;i<NUM;i++){
					for(int j=0; j<NUM; j++){
						if(!flag[i][j]){
						ConvFromCam(AA[i][j],&ScDx[i][j],&ScDy[i][j]);
						}
					}
				}


				if(mode2==0){
					for(int i=0;i<NUM;i++){
						for(int j=0; j<NUM; j++){
							if((!flag[i][j])&&(!flag[i][j+1])&&(!flag[i+1][j])){//&&(GetPixel(ScDx[i][j],ScDy[i][j])==0x000000)
								if(mode==0){
									DrawLine(ScDx[i][j],ScDy[i][j],ScDx[i][j+1],ScDy[i][j+1],GetColor(i,242,255));
								}else{
									DrawLine(ScDx[i][j],ScDy[i][j],ScDx[i+1][j],ScDy[i+1][j],GetColor(200,j,100));
								}
							}
						}
					}
				}else{
						if(mode==0){
							for(int j=0; j<NUM; j++){
								if((!flag[a][b])&&(!flag[a][b+1])&&(!flag[a+1][b])){
								DrawLine(ScDx[a][j],ScDy[a][j],ScDx[a][j+1],ScDy[a][j+1],GetColor(j,242,255));
								
								}
							}
						}else{
							for(int i=0;i<NUM;i++){
								if((!flag[a][b])&&(!flag[a][b+1])&&(!flag[a+1][b])){
								DrawLine(ScDx[i][b],ScDy[i][b],ScDx[i+1][b],ScDy[i+1][b],GetColor(200,i,100));

								}
							}
						}
					
							a++;
							if(a-1>NUM) a=0;
							b++;
							if(b-1>NUM) b=0;
				}


/******************************************ここから移動処理********************************************************/
		
				//作業用ベクトル
		float towardx=Target.x-CamPos.x;
		float towardy=Target.y-CamPos.y;
		float towardz=Target.z-CamPos.z;

				
		if( ( Input & PAD_INPUT_RIGHT ) != 0 ){//右入力
			if( ( Input & PAD_INPUT_A ) != 0 ){//Ｚキー
				//自機移動・見ている方向は同じ
				float tx=towardz; float tz=-towardx;
				ConvVectorSize(&tx,&tz,50);
				CamPos.x+=tx;CamPos.z+=tz;
				Target.x+=tx;Target.z+=tz;
			}else{
				//自機移動せず・見ている方向回転
				Target.x=CamPos.x+(towardx*cos(2/360*PI)+towardz*sin(PI*2/360));
				Target.z=CamPos.z+(towardz*cos(2/360*PI)-towardx*sin(PI*2/360));
			}
		}

		if( ( Input & PAD_INPUT_LEFT ) != 0 ){//左入力
			if( ( Input & PAD_INPUT_A ) != 0 ){
				//自機移動・見ている方向は同じ
				float tx=-towardz; float tz=towardx;
				ConvVectorSize(&tx,&tz,50);
				CamPos.x+=tx;CamPos.z+=tz;
				Target.x+=tx;Target.z+=tz;
			}else{
				//自機移動せず・見ている方向回転
				Target.x=CamPos.x+(towardx*cos(2/360*PI)-towardz*sin(PI*2/360));
				Target.z=CamPos.z+(towardz*cos(2/360*PI)+towardx*sin(PI*2/360));
			}
		}

		if( ( Input & PAD_INPUT_M ) != 0 ){
			float tx=towardx;float ty=towardy;float tz=towardz;
			ConvVectorSize3(&tx,&ty,&tz,15);
			CamPos.x-=tx;CamPos.y-=ty;CamPos.z-=tz;
		}
		if( ( EdgeInput & PAD_INPUT_B ) != 0 ) mode=1-mode;
		if( ( EdgeInput & PAD_INPUT_C ) != 0 ) mode2=1-mode2;


		if( ( Input & PAD_INPUT_UP ) != 0 )  {CamPos.y+=50; Target.y+=50;}
		if( ( Input & PAD_INPUT_DOWN ) != 0 ) {CamPos.y-=50; Target.y-=50; }

		// 裏画面の内容を表画面に反映
		ScreenFlip() ;
	}

	// ＤＸライブラリの後始末
	DxLib_End() ;

	// ソフトの終了
	return 0 ;
}

# disk
ah ye


함수 설명



" using FogOfWarHelperNameSpace; "

= 유니티 해당 c#파일에서 사용할때 using 해줘야됨





" fogOfWarHelper 전장의안개_헬퍼 = new FogOfWarHelper(Index mapSize); "

= 전장의안개 헬퍼 생성 , 맵사이즈는 중간에 바꾸고 싶다면 "변수명.ChangeMapSize(mapSize)" 로 변경가능함





" 전장의안개_헬퍼.AddObstacle(Index obstacleTileIndex); "

= 시야가릴 벽이나 장애물 등록 , 보통 맵초기에 맵에 있는 모든 장애물을 등록 하면 편함





" 전장의안개_헬퍼.AddUnit(Index unitTileIndex, int fob_distance_by, FOB_OPTION_TYPE fOB_OPTION_TYPE); "

= 시야를 가진 유닛등록 , fob_distance = 유닛의 타일단위 시야거리 , fOB_OPTION_TYPE은 정사각형 시야와 원형 시야가 있음





" 전장의안개_헬퍼.Clear_Unit(); "

= 등록한 모든 유닛을 삭제함 , 보통 AddUnit 해도 다음 프레임에서 유닛의 변경된 사항은 적용되지 않기에 유닛의 변화를 적용할려면, 매 프레임 마다 Clear_Unit을 해주고 다시 모든 유닛을 등록하는 방법론을 사용함





" 전장의안개_헬퍼.Update(); "

= 현 상황에 맞게 시야상황을 업데이트함





" FogOfWarHelper_Output 업데이트_결과 = 전장의안개_헬퍼.Output; "

= 업데이트 했던 결과를 받아옴









" FogOfWarHelper_Option 옵션 = new FogOfWarHelper_Option();

옵션.BasicMode();

전장의안개_헬퍼.SetOption(옵션); "

= 옵션을 설정함 ,



softFogMode = 부드러운 안개모드 , false면 output에서 기본 사각형안개만 받고 true이면 다양한 안개타입 받음



prevUpdateUnitList_IndexChangeCheck = false면 update시 무조건 계산하고 , true이면 등록된 유닛들의 상황이 이전 업데이트때 상황과 같다면 계산하지 않고 같은 결과를 줌 최적화용도 내 컴퓨터 기준에선 3~4fps상승? 하는거 같은데 잘 모르겠음. 내부에선 directory 자료구조로 검색해서



thickFogMode = 두꺼운 안개모드 , 기존 안개에서 주변 한 타일 안개를 더 만듬











----------------------------------------------------------------------------------------------------

----------------------------------------------------------------------------------------------------

----------------------------------------------------------------------------------------------------

----------------------------------------------------------------------------------------------------

----------------------------------------------------------------------------------------------------



ex예제. update 함수 형식 예제 (복붙한다고 돌아가는건 아니고 그냥 이런식으로 쓰면된다 정도)




public class A
{

    fogOfWarHelper 전장의안개_헬퍼;



    public void Start()
    {
        전장의안개_헬퍼= new FogOfWarHelper(Index mapSize);

        foreach (Index wallIndex in wallIndexs)
            전장의안개_헬퍼.AddObstacle(wallIndex);
    }




    public void Update()
    {
        FogOfWarHelper_Output prevOutput = 전장의안개_헬퍼.Output;

        foreach (Index i in prevOutput.lightIndexList)
            맵[i.y, i.x].SetFog();

        foreach (FogIndex i in prevOutput.fogIndexList)
            맵[i.index.y, i.index.x].SetFog();

        // ↑ 이전 프레임에서 켜줬던 시야 다시 꺼주기





        전장의안개_헬퍼.Clear_Unit();

        foreach (Unit unit in Units)
        {
            unit.Update();
            전장의안개_헬퍼.AddUnit(unit.GetIndex, unit.fob_distance, unit.fOB_OPTION_TYPE);
        }

        // ↑ 이전 유닛상황은 전부 삭제해주고 현 프레임에 포지션이 바뀐 유닛을 다시 등록



        전장의안개_헬퍼.Update();

        FogOfWarHelper_Output output = 전장의안개_헬퍼.Output;

        foreach (Index i in output.lightIndexList)
            맵[i.y, i.x].SetLight();

        foreach (FogIndex i in output.fogIndexList)
            맵[i.index.y, i.index.x].SetFog(i.fogType);

        // ↑ 업데이트 이후 output을 받아와 시야 밝히는 인덱스들은 켜주고 , 안개 인덱스 들은 꺼줌
    }
}

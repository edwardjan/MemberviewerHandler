package Handler;

import Model.*;
import DBM.*;
import javax.swing.*;

public class MemberViewerHandler
{
    MemberList ml;
    DatabaseManager db;
    MemberListQuery mlq;
    
    public MemberViewerHandler()
    {
        ml = new MemberList();
        db = new DatabaseManager();
        mlq = new MemberListQuery();
    }
    
    public void processMemberList(String ID, String DateFrom, String DateTo)throws Exception
    {
        try
        {
            String dataReceived = db.getData(mlq.getMemberListNameQuery(ID, DateFrom, DateTo),1);
            ml.setMemberListName(dataReceived.split(";"));
            dataReceived = db.getData(mlq.getMemberListDatePeriodQuery(ID, DateFrom, DateTo),1);
            ml.setMemberListDate(dataReceived.split(";"));
            String tableData[][] = new String[ml.getMemberListName().length][ml.getMemberListDate().length + 1];
            for(int x = 0; x < ml.getMemberListName().length; x ++)
            {
                for(int y = 0; y < (ml.getMemberListDate().length + 1); y ++)
                {
                    if(y == 0)
                    {
                        tableData[x][y] = ml.getMemberListName()[x];
                    }
                    else
                    {
                        dataReceived = db.getData(mlq.getMemberListHoursQuery(ID, ml.getMemberListName()[x], ml.getMemberListDate()[y - 1]),1);
                        ml.setMemberListHours(dataReceived.split(";"));
                        if(ml.getMemberListHours()[0].trim().length() < 1)
                        {
                            tableData[x][y] = "No Data";
                        }
                        else
                        {
                            tableData[x][y] = ml.getMemberListHours()[0];
                        }
                    }
                }
            }
            ml.setMemberList(tableData);
        }
        catch(Exception e)
        {
            JOptionPane.showMessageDialog(null, "Could not retrieve data!", "ERROR", JOptionPane.ERROR_MESSAGE);//Display the error occured
        }
    }
    
    public String[][] returnMemberList()
    {
        return ml.getMemberList();
    }
    
    public void processMemberListDate(String ID)throws Exception
    {
        String dataReceived = db.getData(mlq.getMemberListDateQuery(ID),1);
        ml.setMemberListDate(dataReceived.split(";"));
    }
    
    public String[] returnMemberListDate()
    {
        return ml.getMemberListDate();
    }
    
}

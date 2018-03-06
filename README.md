# When_We_Meet
Solve how long we can meet and what is the train we should take.<br>
Sorry, now i am finding input file (time schedule of the trains).<br>

# Purpose
To find out what time of the train should we take.<br>
Where is the station can we meet as long as possibele?<br>
What is the train we should take?<br>
How long can we meet?<br>
What time should we go back?



# Terms
Boy: from Tokyo station

Girl: from Morioka station

Start time: 6:00

curfew: 18:00


# Licence
CopyRight (c) 2018 Shuto Kawabata

Released under the MIT licence

https://opensource.org/licenses/MIT

# Author
Shuto Kawabata

# Code


```c++
#include "stdafx.h"
#include "stdio.h"
#include "string.h"
#include "stdlib.h"

#define MAXCITY 100 

char city_name[MAXCITY][18];
int ncity; //駅数

#define MAXCONN 2000+1 

struct train
{
	int from,to;
	int dpt,arv;
	int fare;
}trains[MAXCONN],rtrains[MAXCONN];

#define NUMBEROFDATASET 100
int dataset_front_num[NUMBEROFDATASET] = {};

#define BIAS 24*60 //24h*60min

int nconn;

#define INFINITE 99999999

int from_hakodate[MAXCITY][MAXCONN];//fr_h
int from_tokyo[MAXCITY][MAXCONN];	//fr_t
int to_hakodate[MAXCITY][MAXCONN];//to_h
int to_tokyo[MAXCITY][MAXCONN];	//to_t

void parse_connection(char *buf);
int city_id(char *name);
int cmp_arv(struct train *t1, 
			struct train *t2);
void prepare_data(int j);
void reverse_data(void);
int change(struct train tv[], int p, int st, int dpttime);
void make_table(int v[MAXCITY][MAXCONN], int org, struct train tv[],int j, int fort);
int min(int a,int b);
int calc_cost(int city,struct train t[],struct train rt[],int d,int departuretime,int arrivetime,int meetingtime,int *temp);
int solve(struct train t[],struct train rt[],int j, char stationx[],char stationy[], int departure,int arrive,int meetingtime,int *start,int *end, int *destinationnum);

int main(int argc, char *argv[])
{

	int i;
	int departuretimehour,departuretimemin,arrivetimehour,arrivetimemin,meetingtime;
	char stationx[18],stationy[18];
	for(i = 1; i < argc; i++ )
	{
		if(argv[i][0] == '-')
		{
			switch( argv[i][1] )
			{
			case'x':
					i++;
					strcpy(stationx, argv[i]);
					break;
			case'y':
					i++;
					strcpy(stationy, argv[i]);
					break;
			case'd':
					i++;
					sscanf(argv[i],"%d:%d",&departuretimehour,&departuretimemin);
					break;
			case'a':
					i++;
					sscanf(argv[i],"%d:%d",&arrivetimehour,&arrivetimemin);
					break;
			case'm':
					i++;
					sscanf(argv[i],"%d",&meetingtime);
					break;
			default:
					fprintf(stderr,"error");
					return 1;
			}
		}
	}

	int departuretime = departuretimehour*60 + departuretimemin;
	int arrivetime = arrivetimehour*60 + arrivetimemin;

	int count;
	char buf[64];

	dataset_front_num[0] = 0;
	i = 1;

	FILE *initialization;
	initialization = fopen("result_table.csv","w");
	fclose(initialization);

	FILE *inputfile,*outputfile;
	inputfile = fopen("sample.in","r");
	outputfile = fopen("result.csv","w");

	fprintf(outputfile,"station1 %s;station2 %s;%d:%d to %d:%d;meeting longs at least %d min\n",stationx,stationy,departuretimehour,departuretimemin,arrivetimehour,arrivetimemin,meetingtime);

	while(1)
	{
		//データセット先頭のデータ数を読む
		if(fgets(buf, sizeof(buf), inputfile) == NULL) {
//			fprintf(outputfile,"error");
			break;
		}//データセット異常終了

		sscanf(buf, "%d",&count); //count<データセット中のデータ数

		dataset_front_num[i] = dataset_front_num[i - 1] + count;
		i++;


		if(count == 0) break; //count = 0 データセットの終了

		while(count-- > 0)
		{
			if(fgets(buf, sizeof(buf), inputfile) == NULL) break;//データセット異常終了
			parse_connection(buf);
		}
	}

	reverse_data();

	struct train rt[MAXCONN],t[MAXCONN];
	int j = 0;
	int k = 0,l = 0;
	int min_cost;
	while(dataset_front_num[j + 1] - dataset_front_num[j] != 0)
	{
		for(k = dataset_front_num[j]; k < dataset_front_num[j+1]; k++)
		{
			t[l].from	= trains[k].from;
			t[l].to		= trains[k].to;
			t[l].dpt	= trains[k].dpt;
			t[l].arv	= trains[k].arv;
			t[l].fare	= trains[k].fare;

			rt[l].from	= rtrains[k].from;
			rt[l].to		= rtrains[k].to;
			rt[l].dpt	= rtrains[k].dpt;
			rt[l].arv	= rtrains[k].arv;
			rt[l].fare	= rtrains[k].fare;

			l++;
		}

		qsort(	t,
			dataset_front_num[j + 1] - dataset_front_num[j],
			sizeof(struct train),
			(int(*)(const void*, const void*))cmp_arv );
		qsort(	rt,
			dataset_front_num[j + 1] - dataset_front_num[j],
			sizeof(struct train),
			(int(*)(const void*, const void*))cmp_arv );
		
		fprintf(outputfile,"dataset %d\n",j+1);
		fprintf(outputfile,"From %s\n",city_name[0]);
		make_table(from_hakodate,city_id("Hakodate"),t,j);
		fprintf(outputfile,"To %s\n",city_name[0]);
		make_table(to_hakodate,city_id("Hakodate"),rt,j);
		fprintf(outputfile,"From Tokyo\n");
		make_table(from_tokyo,city_id("Tokyo"),t,j);
		fprintf(outputfile,"To Tokyo\n");
		make_table(to_tokyo,city_id("Tokyo"),rt,j);
		*/

		int start=0,end=0,destinationnum=0;
		char destination[18];
		int min_cost = solve(t,rt,j,stationx,stationy,departuretime,arrivetime,meetingtime,&start,&end,&destinationnum);
		strcpy(destination, city_name[destinationnum]);
		fprintf(outputfile,"dataset %d,minimum cost is %d,meeting will be held at %s at %d:%d - %d:%d,\n",j+1,min_cost,destination,start/60,start%60,end/60,end%60);

		/*
		int x;
		for(x = 0; x < dataset_front_num[j + 1] - dataset_front_num[j]; x++)
		{
			fprintf(outputfile,"%d,%d,%d,%d,%d\n",x,rt[x].arv,rt[x].dpt,t[x].arv,t[x].dpt);
		}
		*/

		l = 0;
		j++;
			
	}


	fclose(inputfile);
	fclose(outputfile);

    return 0;
}
void parse_connection(char *buf)
{
	char from[18], to[18];
	int dpt[2], arv[2], fare;


	sscanf(buf, "%s %d:%d %s %d:%d %d", from, &(dpt[0]), &(dpt[1]), to, &(arv[0]), &(arv[1]), &fare );

	trains[nconn].from	= city_id(from);
	trains[nconn].to	= city_id(to);
	trains[nconn].dpt	= 60*dpt[0]+dpt[1];
	trains[nconn].arv	= 60*arv[0]+arv[1];
	trains[nconn].fare	= fare;

	nconn++;

}
//ncity
int city_id(char *name)
{
	int i;

	for(i = 0;i < ncity; i++)
	{
		if(strcmp(name, &(city_name[i][0])) == 0 ) return i;
	}

	strcpy(&(city_name[ncity][0]),name);
	return ncity++;
}

int cmp_arv(struct  train *t1, struct train *t2)
{
	return(t1->arv - t2->arv);
}
void prepare_data(int j)
{
	qsort(	trains,
			j,
			sizeof(struct train),
			(int(*)(const void*, const void*))cmp_arv );
}
void reverse_data(void)
{
	int i;

	for(i=0;i<nconn;i++)
	{
		rtrains[i].from	= trains[i].to;
		rtrains[i].to	= trains[i].from;
		rtrains[i].dpt	= BIAS - trains[i].arv;
		rtrains[i].arv	= BIAS - trains[i].dpt;
		rtrains[i].fare	= trains[i].fare;
	}
}


int change(struct train tv[], int p, int st, int dpttime)
{
	while (p >= 0)
	{
		if( (tv[p].to ==st) && (tv[p].arv <= dpttime) )break;
		p--;
	}
	return p;
}


void make_table(int v[MAXCITY][MAXCONN], int org, struct train t[],int j,int fort,int departuretime,int arrivetime)
{
	int ti;
	int i;
	int a;

	int k = 0,l = 0;
	int x,y,z = 0;

	for(i = 0; i < ncity; i++) v[i][0] = INFINITE;

	v[org][0] = 0;

	for(ti = 0;ti < dataset_front_num[j + 1] - dataset_front_num[j]; ti++)
	{
		
		if( t[ti].dpt < departuretime || t[ti].arv > arrivetime )
		{
			for(i = 0; i < ncity;i++) v[i][ti+1] = INFINITE;
			v[org][ti+1] = 0;
			continue;
		}

		for(i = 0; i < ncity;i++) v[i][ti+1] = v[i][ti];

		a = change(t,ti-1,t[ti].from,t[ti].dpt);

		v[t[ti].to][ti+1] = min(v[ t[ti].to ][ti], t[ti].fare + v[ t[ti].from ][ a+1 ]);
	}

	FILE *outputfile;

	outputfile = fopen("result_table.csv","a");

	if(fort == 0) fprintf(outputfile,"from %s\n",city_name[org]);
	else fprintf(outputfile,"to %s\n",city_name[org]);

	fprintf(outputfile,"i,time,time(min),%s,%s,%s\n",city_name[0],city_name[1],city_name[2]);
	fprintf(outputfile,"-1,--:--,--:--,%d,%d,%d\n",v[0][0],v[1][0],v[2][0]);
	for(x = 0; x < dataset_front_num[j + 1] - dataset_front_num[j]; x++)
	{
		fprintf(outputfile,"%d,%d:%d,%d,%d,%d,%d\n",x,t[x].arv/60,t[x].arv%60,t[x].arv,v[0][x+1],v[1][x+1],v[2][x+1]);
	}

}
int calc_cost(int city,struct train t[],struct train rt[],int d,int departuretime,int arrivetime,int meetingtime,int *tempstart,int *tempend)
{
	int a = 0,numt = d;
	d--;
	int stay;
	int c,min_c = INFINITE;
	int i=1;

	while(1)
	{
		if(t[a].dpt < departuretime)
		{
			a++;
			continue;
		}
		
		stay = (BIAS - rt[d].arv) - t[a].arv;
		if(stay < meetingtime)
		{
			d--;
			if(d < 0)break;
			if(rt[d].dpt < (1440 - arrivetime) )break;
			continue;
		}

		c = from_hakodate[city][a+1] + from_tokyo[city][a+1] + to_hakodate[city][d+1] + to_tokyo[city][d+1];
		if(c < min_c)
		{
			min_c = c;
			*tempstart = t[a].arv;
			*tempend = 1440 - rt[d].arv;
			while(c == from_hakodate[city][a+1] + from_tokyo[city][a+1] + to_hakodate[city][d+1-i] + to_tokyo[city][d+1-i])
			{
				if( rt[d-i].dpt > (1440 - arrivetime )) *tempend = 1440 - rt[d-i].arv;
				i++;
			}
		}
		i = 1;
		a++;
		if(a >= numt) break;
	}

	return min_c;
}

int solve(struct train t[],struct train rt[],int j, char stationx[],char stationy[], int departure,int arrive,int meetingtime,int *start,int *end, int *destinationnum)
{
	int c,a_i;
	int cost,min_cost;
	int tempstart=0,tempend=0;

	prepare_data(j);
	make_table(from_hakodate,city_id(stationx)		, t	,	j,	0,	departure		,		arrive);
	make_table(from_tokyo,city_id(stationy)			, t	,	j,	0,	departure		,		arrive);
	make_table(to_hakodate,city_id(stationx)		, rt,	j,	1,	BIAS - arrive	,		BIAS - departure);
	make_table(to_tokyo,city_id(stationy)			, rt,	j,	1,	BIAS - arrive	,		BIAS - departure);

	min_cost = INFINITE;
	for(c = 0;c < ncity; c++)
	{
		cost = calc_cost(c,t,rt,dataset_front_num[j + 1] - dataset_front_num[j],departure,arrive,meetingtime,&tempstart,&tempend);
		if(cost < min_cost) 
		{
			min_cost = cost;
			*start = tempstart;
			*end = tempend;
			*destinationnum = c;
		}
	}
	if(min_cost >= INFINITE) min_cost = 0;

	return min_cost;
}

int min(int a,int b)
{
	int minimum;

	if(a < b) minimum = a;
	else if(a > b) minimum = b;
	else minimum = a;

	return minimum;
}
```

###Данные по branch для csv я брал из функции void Fetch<FuncInstr>::clock( Cycle cycle) в файле fetch.cpp :



template <typename FuncInstr>
void Fetch<FuncInstr>::clock( Cycle cycle)
{
clock_bp( cycle);

/* getting PC */
auto target = get_cached_target( cycle);

/* push bubble */
if ( !target.valid)
return;

/* hold PC for the stall case */
wp_hold_pc->write( target, cycle);

auto bp_info = bp->get_bp_info( target.address);
Instr instr( memory->fetch_instr( target.address), bp_info);
instr.set_sequence_id( target.sequence_id);

/* set next target according to prediction */
wp_target->write( instr.get_predicted_target(), cycle);

/* log */
sout « "fetch cycle " « std::dec « cycle « ": " « instr « " " « bp_info « std::endl;

csvfile « std::dec « bp_info.pc « ',' « bp_info.target « ',' « bp_info.is_taken « std::endl;

/* sending to decode */
wp_datapath->write( std::move( instr), cycle);
}





###Cоздание csv файла происходит в файле log.h в классе Log



class Log
{
public:
mutable LogOstream sout;
mutable LogOstream serr;
std::ofstream csvfile;

Log() : sout( std::cout), serr( std::cerr) 
{ 
csvfile.open("~/large_dataset.csv");
}

// Rule of five
virtual ~Log();
Log( const Log&) = delete;
Log( Log&&) = delete;
Log& operator=( const Log&) = delete;
Log& operator=( Log&&) = delete;
};
'
